FROM python:3.14-slim AS builder
WORKDIR /app
COPY pyproject.toml uv.lock ./
RUN pip install uv && uv sync --frozen

FROM python:3.14-slim 
WORKDIR /app
COPY --from=builder /app ./
RUN pip install uv
COPY . . 
RUN useradd --create-home appuser
USER appuser

EXPOSE 8080
CMD uv run gunicorn -b 0.0.0.0:$PORT "frontend:create_app()"