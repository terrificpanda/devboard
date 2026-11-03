FROM dhi.io/node:26-dev AS build

WORKDIR /app

COPY package*.json ./

RUN npm install --legacy-peer-deps

RUN npm list vite > /dev/null 2>&1 || npm install --no-save vite@5.4.11

COPY . .

# Browser tracing is baked in at build time (Vite replaces import.meta.env).
# Off by default so a plain `docker build` or compose run stays untraced.
ARG VITE_OTEL_ENABLED=false
ARG VITE_OTEL_ENVIRONMENT=dev
ARG VITE_APP_VERSION=dev
ENV VITE_OTEL_ENABLED=$VITE_OTEL_ENABLED \
    VITE_OTEL_ENVIRONMENT=$VITE_OTEL_ENVIRONMENT \
    VITE_APP_VERSION=$VITE_APP_VERSION

RUN npm run build

RUN chmod -R u=rwX,go=rX /app/dist

FROM dhi.io/node:26

WORKDIR /app

COPY --from=build --chown=1000:1000 /app/dist/ ./dist/

COPY --from=build --chown=1000:1000 /app/node_modules ./node_modules

COPY  --chown=1000:1000 --chmod=0644 vite.preview.config.js ./vite.config.js

USER 1000:1000

EXPOSE 4173

ENTRYPOINT ["node", "--no-warnings"]
CMD ["./node_modules/.bin/vite", "preview", "--host", "0.0.0.0", "--port", "4173"]
