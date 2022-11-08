FROM rockylinux:8.6.20227707

RUN dnf update -qy &&\
dnf install epel-release -qy &&\
dnf install ansible -qy

WORKDIR /ansible_files

CMD ["ansible", "--version"]
