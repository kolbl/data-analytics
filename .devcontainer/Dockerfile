FROM mcr.microsoft.com/devcontainers/python:1-3.13-bookworm

USER vscode
RUN curl -LsSf https://astral.sh/uv/0.13.0/install.sh | sh -s

ENV PATH="/home/vscode/.local/bin:${PATH}"

RUN uv tool install "pypiserver[passlib]"
