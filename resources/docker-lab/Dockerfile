#FROM registry.access.redhat.com/ubi8/openjdk-17
FROM maven:3-jdk-11

#RUN microdnf install podman && podman clean all

USER root
run apt-get update
run apt-get install docker.io -y
run docker --version
