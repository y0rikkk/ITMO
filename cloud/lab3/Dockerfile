FROM ubuntu:latest

RUN apt-get update && apt-get install -y git

RUN echo "hello" > /hello.txt

RUN git config --global user.email "yarick2205@gmail.com"
RUN git config --global user.name "y0rikkk"
RUN git clone https://github.com/y0rikkk/ITMO.git /repo
RUN cp /hello.txt /repo
RUN cd /repo && git add hello.txt
RUN cd /repo && git commit -m "Add hello.txt"
RUN cd /repo && git push origin main
