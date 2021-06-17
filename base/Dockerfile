FROM python:3.9.5-slim

RUN apt-get update && apt-get -y install apt-utils && apt-get -y install gcc openslide-tools curl tini git

RUN git clone https://github.com/msk-mind/data-processing.git

RUN python -m pip install --upgrade pip && pip install numpy 

RUN cd data-processing && pip install -e .

RUN pip install dask distributed jupyter bokeh --upgrade
   
COPY prepare.sh /usr/bin/prepare.sh

RUN mkdir /opt/app

ENTRYPOINT ["tini", "-g", "--", "/usr/bin/prepare.sh"]
