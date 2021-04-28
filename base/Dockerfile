FROM python:3.9.4-slim

RUN apt-get update && apt-get -y install gcc openslide-tools curl tini

# Infrustructure packages
RUN pip3 install \
    click \
    minio \
    neo4j \
    numpy>=1.9.0 \
    pandas>=1.1.0 \
    pyarrow>=3.0.0 \
    pyspark

# Speciality packages
RUN pip3 install \
    medpy \
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
    orjson \
    filehash \
    bokeh

# Exec packages
RUN pip3 install dask distributed jupyter --upgrade

RUN curl -L -o data-processing.tar.gz https://github.com/msk-mind/data-processing/archive/refs/tags/v0.1.tar.gz \
    && tar xzvf data-processing.tar.gz \
    && rm data-processing.tar.gz

ENV PYTHONPATH=/data-processing-0.1:$PYTHONPATH
   
COPY prepare.sh /usr/bin/prepare.sh

RUN mkdir /opt/app

ENTRYPOINT ["tini", "-g", "--", "/usr/bin/prepare.sh"]
