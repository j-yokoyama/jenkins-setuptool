FROM centos:7

RUN yum install -y \
      ansible \
      rubygems

RUN gem install net-ssh --version "=3.2.0"
RUN gem install serverspec
RUN gem install rake --version "=12.3.3"
