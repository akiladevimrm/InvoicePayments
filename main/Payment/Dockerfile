FROM alpine/git as clone
WORKDIR /app
RUN git clone https://github.com/akiladevimrm/AOTCustomerPayment.git


FROM openjdk:8-jdk-alpine
WORKDIR /app
CMD ["java"]

FROM maven:3.6.3-ibmjava-8-alpine as build
WORKDIR /app/apps/AOTCustomerPayment
COPY --from=0 /app/AOTCustomerPayment /app/apps/AOTCustomerPayment
RUN mvn clean install -DskipTests

RUN mkdir -pv /app/apps/AOTCustomerPayment/Inbound
RUN mkdir -pv /app/apps/AOTCustomerPayment/Outbound


CMD ["java", "-jar", "/app/apps/AOTCustomerPayment/target/AOTCustomerPayment-1.0-jar-with-dependencies.jar"]

