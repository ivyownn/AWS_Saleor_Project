# Step 1: Use Python 3.12 as the base image
FROM python:3.12

# Step 2: Install system dependencies
RUN apt-get update && apt-get install -y \
    gettext \
    libffi8 \
    libgdk-pixbuf2.0-0 \
    liblcms2-2 \
    libopenjp2-7 \
    libssl3 \
    libtiff6 \
    libwebp7 \
    shared-mime-info \
    mime-support \
    libpq5 \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Step 3: Set up Python dependencies
ENV POETRY_VERSION=1.8.4
RUN pip install "poetry==$POETRY_VERSION"

# Configure Poetry to install dependencies in the system environment
RUN poetry config virtualenvs.create false

# Set the working directory
WORKDIR /app

# Copy entire project (including manage.py)
COPY . .

# Install dependencies without installing the project itself
RUN poetry install --no-root --no-interaction --no-ansi

# Step 4: Create a non-root user
RUN groupadd -r saleor && useradd --no-log-init -r -g saleor saleor
RUN chown -R saleor:saleor /app

# Step 5: Configure static files
ARG STATIC_URL
ENV STATIC_URL=${STATIC_URL:-/static/}
RUN SECRET_KEY=dummy STATIC_URL=${STATIC_URL} python3 manage.py collectstatic --no-input

# Step 6: Expose application port
EXPOSE 8000

# Step 7: Set entrypoint command
CMD ["gunicorn", "--bind", ":8000", "--workers", "4", "--worker-class", "saleor.asgi.gunicorn_worker.UvicornWorker", "saleor.asgi:application"]

#Build the Docker Image: docker build -t saleor-api:latest ./saleor-api
#Run the Container: docker run -p 8000:8000 saleor-api:latest

#Modify the Dockerfile to include COPY . .
#Rebuild the image:docker build --no-cache -t saleor-api:latest ./saleor-api

