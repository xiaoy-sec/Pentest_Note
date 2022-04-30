	>wmic /node:192.168.1.2 /USER:administrator PATH win32_terminalservicesetting WHERE (__Class!="") CALL SetAllowTSConnections 1
