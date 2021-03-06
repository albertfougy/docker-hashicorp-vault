FROM cnadiminti/dynamodb-local:2017-04-22_beta

MAINTAINER Ivan Suftin <isuftin@usgs.gov>

RUN apt-get update

RUN apt-get -y install python python-pip libyaml-dev python-dev

RUN apt-get -y autoclean && apt-get -y --purge autoremove

COPY /data/certs/${ROOT_FILENAME:-root.pem} /root.pem

# On networks that do not use SSL intercept, using the --cert flag will fail
# pip installations. So here we try with and without certs 
RUN pip --cert /root.pem install --upgrade pip || pip install --upgrade pip

RUN pip --cert /root.pem install awscli || pip install awscli

ENV AWS_ACCESS_KEY_ID ${AWS_ACCESS_KEY_ID:-"anything"}
ENV AWS_SECRET_ACCESS_KEY ${AWS_SECRET_ACCESS_KEY:-"anything"}
ENV AWS_DEFAULT_REGION ${AWS_DEFAULT_REGION:-"us-east-1"}

ENTRYPOINT ["java"]

CMD ["-Djava.library.path=.", "-jar", "DynamoDBLocal.jar",  "--sharedDb", "-optimizeDbBeforeStartup", "-dbPath", "/dynamodb_local_db", "-port", "8000", "-cors", "*"]

HEALTHCHECK --interval=30s --timeout=3s CMD aws dynamodb list-tables --endpoint-url http://127.0.0.1:${DYNAMODB_PORT:-8000} || exit 1
