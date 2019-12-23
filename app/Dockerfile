FROM pytorch/pytorch:0.4.1-cuda9-cudnn7-devel

RUN apt update \
  && apt install -y mecab libmecab-dev mecab-ipadic-utf8 sudo curl file \
  && apt clean \
  && rm -rf /var/lib/apt/lists/*
RUN pip install mecab-python3

WORKDIR /tmp
RUN git clone --depth 1 https://github.com/neologd/mecab-ipadic-neologd.git
WORKDIR ./mecab-ipadic-neologd
RUN sudo ./bin/install-mecab-ipadic-neologd -n -y

RUN mkdir /var/www
WORKDIR /var/www

RUN pip install --upgrade pip && pip install --no-cache-dir -r requirements.txt

CMD ["uwsgi","--ini","/var/www/uwsgi.ini"]