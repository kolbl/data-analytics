FROM mcr.microsoft.com/devcontainers/python:1-3.13-bookworm

# CVE-2026-111497: curl pulls in libbrotli1. Bookworm's fix is 1.0.9-2+deb12u1.
USER root
RUN rm -f /etc/apt/sources.list.d/yarn.list \
    && apt-get update \
    && apt-get install -y --no-install-recommends libbrotli1 \
    && rm -rf /var/lib/apt/lists/*

USER vscode
RUN curl -LsSf https://astral.sh/uv/0.13.0/install.sh | sh -s

ENV PATH="/home/vscode/.local/bin:${PATH}"

RUN uv tool install "pypiserver[passlib]"
