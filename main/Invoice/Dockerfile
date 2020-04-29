FROM alpine/git as clone
WORKDIR /app
RUN git clone https://github.com/akiladevimrm/AOTVendorInvoicing.git


FROM openjdk:8-jdk-alpine
WORKDIR /app
CMD ["java"]

FROM maven:3.6.3-ibmjava-8-alpine as build
WORKDIR /app/apps/AOTVendorInvoicing
COPY --from=0 /app/AOTVendorInvoicing /app/apps/AOTVendorInvoicing
RUN mvn clean install -DskipTests

RUN mkdir -pv /app/apps/AOTVendorInvoicing/Inbound
RUN mkdir -pv /app/apps/AOTVendorInvoicing/Outbound


CMD ["java", "-jar", "/app/apps/AOTVendorInvoicing/target/AOTVendorInvoicing-1.0-jar-with-dependencies.jar"]

