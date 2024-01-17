FROM python:3.8-slim-buster

RUN apt-get update && \
        apt-get upgrade -y && \
        apt-get install -y \
        build-essential \
        gcc \
        g++ \
        make \
        cmake \
        grass \
        grass-dev \        
        libc-dev \
        libxslt-dev \
        libxml2-dev \
        libffi-dev \
        libssl-dev \
        zip \
        unzip \
        vim \
        curl \
        libgdal-dev \
        gdal-bin \
        && rm -rf /var/lib/apt/lists/* \
        && apt-cache search linux-headers-generic

ENV CPLUS_INCLUDE_PATH=/usr/include/gdal
ENV C_INCLUDE_PATH=/usr/include/gdal

RUN ogrinfo --version

RUN gdal-config --version

RUN pip install --upgrade --no-cache-dir setuptools==58.0.2
RUN pip install --upgrade --ignore-installed pip wheel six \
    && pip install --upgrade --no-cache-dir --ignore-installed \
        awslambdaric \
        boto3 \
        redis \
        httplib2 \
        requests \
        numpy \
        scipy \
        pandas \
        pika \
        kafka-python \
        cloudpickle \
        ps-mem \
        tblib

COPY requirements.txt requirements.txt
RUN export CPLUS_INCLUDE_PATH=/usr/include/gdal && \
    export C_INCLUDE_PATH=/usr/include/gdal && \
    pip install --upgrade pip six && pip install --no-cache-dir -r requirements.txt

WORKDIR /root

RUN curl -sSL http://step.esa.int/thirdparties/sen2cor/2.9.0/Sen2Cor-02.09.00-Linux64.run -o Sen2Cor-02.09.00-Linux64.run && \
    chmod +x Sen2Cor-02.09.00-Linux64.run && \
    bash Sen2Cor-02.09.00-Linux64.run --target /opt/sen2cor && \
    rm Sen2Cor-02.09.00-Linux64.run

# Setting environment variables
ENV SEN2COR_PATH=/opt/sen2cor \
    SEN2COR_HOME=/home/user/sen2cor/2.9

ENV SEN2COR_BIN=$SEN2COR_PATH/lib/python2.7/site-packages/sen2cor \
    PATH=$SEN2COR_PATH/bin:${PATH} \
    LD_LIBRARY_PATH=$SEN2COR_PATH/lib:${LD_LIBRARY_PATH} \
    GDAL_DATA=$SEN2COR_PATH/share/gdal \
    GDAL_DRIVER_PATH=disable \
    LC_NUMERIC=C \
    PYTHONUNBUFFERED=1

# Copy Lithops proxy and lib to the container image.
ENV APP_HOME /lithops
WORKDIR $APP_HOME


# Add Lithops
COPY lithops_lambda.zip $APP_HOME
RUN unzip lithops_lambda.zip \
    && rm lithops_lambda.zip \
    && mkdir handler \
    && touch handler/__init__.py \
    && mv entry_point.py handler/


ENTRYPOINT [ "/usr/local/bin/python", "-m", "awslambdaric" ]
CMD [ "handler.entry_point.lambda_handler" ]




