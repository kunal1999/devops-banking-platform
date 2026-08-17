FROM python:3.14-slim AS BUILDER    
WORKDIR /app
COPY pyproject.toml uv.lock  ./
RUN pip install uv && uv sync --frozen

FROM python:3.14-slim
WORKDIR /app
COPY  --from=BUILDER /app /app
COPY . .    
EXPOSE 8080
CMD ["gunicorn", "-b", "0.0.0.0:8080", "userservice:app"]
