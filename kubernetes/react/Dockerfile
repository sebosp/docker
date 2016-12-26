FROM node:7.1.0
MAINTAINER Sebastian Ospina <kraige@gmail.com>
ENV L0_CACHE 20161225
RUN groupadd webuser -g 1000 && useradd webuser -m -u 1000 -g 1000
ADD yarn-latest.tar.gz /opt
ENV PATH "$PATH:/opt/dist/bin:~/.npm-global/bin"
ADD babelrc /home/webuser/.babelrc
ADD app/package.json /usr/src/app/
RUN mkdir -p /usr/src/app && \
    chown webuser:webuser /usr/src/app && \
    mkdir /home/webuser/.npm-global && \
    npm config set prefix "/home/webuser/.npm-global" && \
    cd /usr/src/app/ && \
    /opt/dist/bin/yarn install --no-emoji --no-progress && \
    chmod -R a+r /usr/src/app/node_modules && \
    chmod -R a+r /home/webuser/.npm-global && \
    mkdir /usr/src/app/node_modules/.cache && \
    chown webuser:webuser /usr/src/app/node_modules/.cache
USER webuser
WORKDIR /usr/src/app
RUN npm build
EXPOSE 3000
CMD npm start
