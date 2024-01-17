FROM python:3.8-buster

RUN apt-get update && \
        apt-get upgrade -y && \
        apt-get install -y --no-install-recommends \
        build-essential \
        gcc \
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
        libgdal-dev \
        gdal-bin \
        && rm -rf /var/lib/apt/lists/* \
        && apt-cache search linux-headers-generic

ENV CPLUS_INCLUDE_PATH=/usr/include/gdal
ENV C_INCLUDE_PATH=/usr/include/gdal
ENV GRASSBIN=grass76

COPY requirements.txt requirements.txt 
RUN pip install --upgrade --no-cache-dir setuptools==58.0.2
RUN pip install --upgrade pip six wheel && \
    pip install --upgrade --no-cache-dir --ignore-installed \
        awslambdaric &&\
    pip install --no-cache-dir -r requirements.txt


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
