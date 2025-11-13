FROM python:3.12-slim

ENV PYTHONUNBUFFERED=1

WORKDIR /app

# Copy requirements first so Docker caches installs when requirements don't change
COPY requirements.txt ./

# Install minimal build deps only while installing wheels that need compilation,
# then remove them to keep the image small. Fail the build if pip install fails.
RUN set -eux; \
	if [ -f requirements.txt ]; then \
		apt-get update && apt-get install -y --no-install-recommends build-essential gcc && \
		pip install --no-cache-dir -r requirements.txt && \
		apt-get purge -y --auto-remove build-essential gcc && rm -rf /var/lib/apt/lists/*; \
	else \
		pip install --no-cache-dir flask flask-cors PyJWT pymongo numpy scipy scikit-learn python-dotenv gunicorn; \
	fi

# Copy app source
COPY kMeansClusteringAlgorithum.py ./

EXPOSE 5000

ENV FLASK_ENV=production
ENV PORT=5000

# Use gunicorn for production; module `kMeansClusteringAlgorithum` exposes `app`
CMD ["gunicorn", "-w", "2", "-b", "0.0.0.0:5000", "kMeansClusteringAlgorithum:app"]