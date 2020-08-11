FROM python:alpine
RUN adduser -G users -u 1001 -h /app -D app
USER app
WORKDIR /app
CMD /usr/local/bin/python -m http.server 8000

