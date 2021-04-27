FROM python:3.9.4-buster

RUN pip3 install \
    lz4 \
    click \
    medpy \
    minio \
    neo4j \
    numpy>=1.9.0 \
    pandas>=1.1.0 \
    pyarrow>=3.0.0 \
    pytest

RUN pip3 install \
    scikit-image \
    itk \
    opencv-python \
    openslide-python \
    checksumdir \
    pyinstaller>=4.0 \
    PyYAML>=5.4 \
    jsonpath-ng>=1.5.2 \
    yamale>=3.0.4 \
    pydicom>=2.1.0 \
    pyradiomics>=3.0.1 \
    requests>=2.25.1 \
    Pillow>=8.1.1 \
    shapely \
    seaborn \
    ijson \
    geojson \
    orjson

RUN pip3 install bokeh --upgrade

RUN pip3 install dask distributed --upgrade

RUN apt-get update &&  apt-get -y install gcc && apt-get install -y openslide-tools	

RUN apt-get -y install tini

RUN curl -L -o data-processing.tar.gz https://github.com/msk-mind/data-processing/archive/refs/tags/v0.1.tar.gz

RUN tar xzvf data-processing.tar.gz

ENV PYTHONPATH=/data-processing-0.1:$PYTHONPATH

ENV PATH=$PATH
   
COPY prepare.sh /usr/bin/prepare.sh

RUN mkdir /opt/app

RUN pip3 install pyspark filehash --upgrade

RUN pip3 install jupyter --upgrade 

ENTRYPOINT ["tini", "-g", "--", "/usr/bin/prepare.sh"]
