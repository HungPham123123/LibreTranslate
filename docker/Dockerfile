FROM python:3.11.9-slim-bullseye as builder

WORKDIR /app

RUN apt-get update -qq \
  && apt-get install --no-install-recommends -y pkg-config gcc g++ \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/*

RUN python -mvenv venv && ./venv/bin/pip install --no-cache-dir --upgrade pip

COPY . .

RUN ./venv/bin/pip install Babel==2.12.1 \
  && ./venv/bin/pip install torch==2.0.1 --extra-index-url https://download.pytorch.org/whl/cpu \
  && ./venv/bin/pip install "numpy<2" \
  && ./venv/bin/pip install . \
  && ./venv/bin/pip cache purge

FROM python:3.11.9-slim-bullseye

RUN addgroup --system --gid 1032 libretranslate && adduser --system --uid 1032 libretranslate && mkdir -p /home/libretranslate/.local && chown -R libretranslate:libretranslate /home/libretranslate/.local
USER libretranslate

COPY --from=builder --chown=1032:1032 /app /app
WORKDIR /app

COPY --from=builder --chown=1032:1032 /app/venv/bin/ltmanage /usr/bin/

EXPOSE 5000
ENTRYPOINT [ "./venv/bin/libretranslate", "--host", "*" ]
