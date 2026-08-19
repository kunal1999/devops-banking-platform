FROM python:3.14-slim AS builder 
WORKDIR /app
COPY pyproject.toml uv.lock ./
RUN pip install uv && uv sync --frozen

FROM python:3.14-slim AS stage
WORKDIR /app
COPY --from=builder /app ./
RUN pip install uv
COPY . .
EXPOSE 8081
CMD ["uv","run","gunicorn", "-b", "0.0.0.0:8081", "contacts:create_app()"]
