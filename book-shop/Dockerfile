FROM python:3.11-slim

ARG ARTIFACT_NAME

WORKDIR /app

ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

COPY artifacts/${ARTIFACT_NAME} /tmp/app.tar.gz

RUN tar -xzf /tmp/app.tar.gz -C /app --strip-components=1 \
    && rm /tmp/app.tar.gz

RUN pip install --upgrade pip \
    && pip install --no-cache-dir -r requirements.lock.txt

EXPOSE 8000

CMD ["gunicorn", "book_shop.wsgi:application", "--bind", "0.0.0.0:8000"]
