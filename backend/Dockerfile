FROM python:3.11-slim

WORKDIR /app

# Install uv
RUN pip install uv

# Copy pyproject.toml if it exists, otherwise create a simple one
COPY pyproject.toml* ./
RUN uv sync

COPY . .

EXPOSE 8000

CMD ["uv", "run", "uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
