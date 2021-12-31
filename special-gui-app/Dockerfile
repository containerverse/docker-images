FROM ubuntu:20.04

LABEL author="Luis Acosta"

RUN apt-get update && apt-get install -y xauth
RUN apt-get install -y firefox

EXPOSE 8887
CMD ["/usr/bin/firefox"]
