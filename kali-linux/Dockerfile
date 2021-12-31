FROM kalilinux/kali-rolling

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get -y update && apt-get -y dist-upgrade && apt-get -y autoremove && apt-get clean
# install Kali Linux metapackages and vulnerabilities scanners
RUN apt-get -y install kali-linux kali-tools-wireless kali-tools-top10 exploitdb dirb nikto wpscan uniscan
# Some system tools
RUN apt-get -y install aircrack-ng crackmapexec crunch dirb dirbuster dnsenum dnsrecon dnsutils dos2unix enum4linux exploitdb ftp git gobuster hashcat hping3 hydra impacket-scripts john joomscan masscan metasploit-framework mimikatz nasm ncat netcat-traditional nikto nmap patator powersploit proxychains python-impacket python-pip python3 recon-ng responder samdump2 snmp socat sqlmap sslscan sslstrip theharvester vim wafw00f weevely wfuzz whois wordlists --no-install-recommends

RUN apt-get -y unzip curl telnet strace ltrace tmate build-essential wget proxychains proxychains4 zstd net-tools kmod pciutils wifite ruby metasploit-framework

# alias
RUN echo "alias l='ls -al'" >> /root/.bashrc
RUN echo "alias nse='ls /usr/share/nmap/scripts | grep '" >> /root/.bashrc
RUN echo "alias scan-range='nmap -T5 -n -sn'" >> /root/.bashrc
RUN echo "alias http-server='python3 -m http.server 8080'" >> /root/.bashrc
RUN echo "alias php-server='php -S 127.0.0.1:8080 -t .'" >> /root/.bashrc
RUN echo "alias ftp-server='python -m pyftpdlib -u \"admin\" -P \"S3cur3d_Ftp_3rv3r\" -p 2121'" >> /root/.bashrc

# initialize Metasploit databse
RUN service postgresql start && msfdb init && service postgresql stop

#VOLUME /root /var/lib/postgresql
# default LPORT for reverse shell
EXPOSE 4444/tcp

WORKDIR /root
CMD ["/bin/bash"]
