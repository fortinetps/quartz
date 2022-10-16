#-----------------------------------------------------------------------------------------
# Copyright (c) Fortinet Corporation. All rights reserved.
# Licensed under the MIT License. See LICENSE in the project root for license information.
#-----------------------------------------------------------------------------------------

FROM golang:alpine3.16
USER root
RUN apk update
RUN apk add --no-cache openssh screen git make curl nfs-utils hugo
RUN go install github.com/jackyzha0/hugo-obsidian@latest

RUN /usr/bin/ssh-keygen -A
RUN echo 'root:secretpassword' | chpasswd
RUN sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/g' /etc/ssh/sshd_config 
RUN sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/g' /etc/ssh/sshd_config 

EXPOSE 22 1313