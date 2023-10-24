FROM ubuntu:latest

RUN apt-get update && apt-get install -y git

RUN echo "hello" > /hello.txt

RUN git config --global user.email "yarick2205@gmail.com"
RUN git config --global user.name "y0rikkk"

ARG GITHUB_TOKEN
ENV GITHUB_TOKEN=${GITHUB_TOKEN}

RUN git clone https://github.com/y0rikkk/ITMO.git /repo
RUN cp /hello.txt /repo
WORKDIR /repo
RUN git add hello.txt
RUN git commit -m "Add hello.txt"
RUN git push origin main
