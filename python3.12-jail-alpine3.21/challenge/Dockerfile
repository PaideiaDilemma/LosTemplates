# Alpine 3.21.3 (base system)
# Python 3.12

###############
# Base system #
###############
FROM docker.io/library/alpine@sha256:a8560b36e8b8210634f77d9f7f9efd7ffa463e380b75e2e74aff4511df3ef88c AS base

# Install apt dependencies if needed
RUN apk update && apk add --no-cache socat python3 py3-pip coreutils file && rm -rf /var/cache/apk/*

# Enable the virtual enviroment
RUN python3 -m venv /.venv
ENV PATH="/.venv/bin:$PATH"

# Install pip dependencies
COPY requirements.txt /app/requirements.txt
RUN pip install --no-cache-dir --upgrade -r /app/requirements.txt 

#################
# Runner system #
#################
# Opensuse Tumbleweed (only for running nsjail)
FROM docker.io/opensuse/tumbleweed@sha256:404df0954fda18f5d823ed520a41ff6e60d7ab00b2ef688477b679c2ff742560 

# Install apk dependencies if needed
RUN zypper -n install nsjail fakeroot && zypper -n clean --all

# Copy base filesystem
COPY --from=base / /jail

# Copy challenge required files
RUN mkdir -p /jail/app
COPY challenge /jail/app/challenge
RUN chmod +x /jail/app/challenge
COPY flag.txt /jail/flag.txt
COPY entrypoint.sh /jail/app/entrypoint.sh

# add "-m none:/DESTPATH:tmpfs:size=N" before --cwd on nsjail args to have a tmpfs-backed writable DESTPATH of N bytes
# remember that /DESTPATH cannot contain any files coming from /jail (as its a mount). If you want 
# pre-created/copied files in /DESTPATH you should manually copy them in entrypoint.sh
# Note: /DESTPATH should not contain /jail as a prefix
SHELL ["/bin/sh", "-c"]
CMD nsjail \
    --mode l \
    --disable_proc \
    --time_limit ${TIMEOUT} \
    --bindhost 0.0.0.0 \
    --port 1337 \
    --bindmount_ro /jail:/ \
    -m none:/dev:tmpfs:mode=555,size=1,uid=65534,gid=65534 \
    -R /dev/urandom \
    -R /dev/random \
    -B /dev/null \
    -R /dev/zero \
    --cwd /app/ \
    -u 1337:1337:1 \
    -g 1337:1337:1 \
    -u 65534:65534:1 \
    -g 65534:65534:1 \
    -- /app/entrypoint.sh
