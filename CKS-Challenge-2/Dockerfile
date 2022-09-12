FROM python:3.6-alpine

## Install Flask
RUN pip install flask

## Copy All files to /opt
COPY ./app /opt/

## Flask app to be exposed on port 8080
EXPOSE 8080

## Flask app to be run as 'worker'
RUN adduser -D worker

## Expose port 22
EXPOSE 22

WORKDIR /opt

USER worker

ENTRYPOINT ["python", "app.py"]
