# Alpine 3.21.3 (base system)

###############
# Base system #
###############
FROM docker.io/library/alpine@sha256:a8560b36e8b8210634f77d9f7f9efd7ffa463e380b75e2e74aff4511df3ef88c

# Install apt dependencies if needed
RUN apk update && apk add --no-cache socat python3 py3-pip coreutils && rm -rf /var/cache/apk/*

# Enable the virtual enviroment
RUN python3 -m venv /.venv
ENV PATH="/.venv/bin:$PATH"

# Install pip dependencies
COPY requirements.txt /app/requirements.txt
RUN pip install --no-cache-dir --upgrade -r /app/requirements.txt 

# Copy challenge required files
RUN mkdir -p /app
COPY entrypoint.sh /app
COPY flag.txt /flag.txt
COPY src/ /app
RUN chmod +x /app/app.py

SHELL ["/bin/sh", "-c"]
CMD /app/entrypoint.sh
