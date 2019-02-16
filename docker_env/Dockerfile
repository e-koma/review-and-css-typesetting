FROM ekoma/docker-review:latest

ARG VIVLIOSTYLE_VERSION
WORKDIR /repository_root

RUN apt-get -y update || true \
 && apt-get -y upgrade \
 && apt-get -y update \
 && apt-get -y install \
    build-essential \
    curl \
    libgconf-2-4 \
    libxml2 \
    libxml2-dev \
    libxslt1-dev \
    procps \
    ruby-dev \
    unzip \
    wget \
    zlib1g-dev \
 && mkdir -p /vivliostyle \
 && cd /vivliostyle \
 && rm -rf /usr/local/bin/vivliostyle-server \
 && wget https://github.com/vivliostyle/vivliostyle.js/releases/download/${VIVLIOSTYLE_VERSION}/vivliostyle-js-${VIVLIOSTYLE_VERSION}.zip \
 && unzip vivliostyle-js-${VIVLIOSTYLE_VERSION}.zip \
 && cp ./vivliostyle-js-${VIVLIOSTYLE_VERSION}/start-webserver /usr/local/bin/vivliostyle-server \
 && rm -rf vivliostyle-js-${VIVLIOSTYLE_VERSION}.zip \
 && cd /repository_root \
 && npm install -g n \
 && n v11.6.0 \
 && npm i -g npm@6.6.0 \
# Install latest chrome dev package and fonts to support major charsets (Chinese, Japanese, Arabic, Hebrew, Thai and a few others)
# Note: this installs the necessary libs to make the bundled version of Chromium that Puppeteer
# installs, work.
 && apt-get update \
 && apt-get install -y wget --no-install-recommends \
 && wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | apt-key add - \
 && sh -c 'echo "deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google.list' \
 && apt-get update \
 && apt-get install -y \
    fonts-ipafont-gothic \
    fonts-kacst \
    fonts-thai-tlwg \
    fonts-wqy-zenhei \
    google-chrome-unstable \
    ttf-freefont \
	--no-install-recommends \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/* \
 && apt-get purge --auto-remove -y curl \
 && rm -rf /src/*.deb

# It's a good idea to use dumb-init to help prevent zombie chrome processes.
ADD https://github.com/Yelp/dumb-init/releases/download/v1.2.0/dumb-init_1.2.0_amd64 /usr/local/bin/dumb-init
RUN chmod +x /usr/local/bin/dumb-init \
# Uncomment to skip the chromium download when installing puppeteer. If you do,
# you'll need to launch puppeteer with:
#     browser.launch({executablePath: 'google-chrome-unstable'})
# ENV PUPPETEER_SKIP_CHROMIUM_DOWNLOAD true
 && rm -rf /etc/apt/sources.list.d/google.list \
 && apt-get update \
 && apt-get -qq -y install \
    curl \
# fonts
    fonts-ipa* \
    fonts-ricty-diminished \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*

COPY startup.sh /startup.sh
RUN chmod +x /startup.sh

ENTRYPOINT [ "/startup.sh" ]
