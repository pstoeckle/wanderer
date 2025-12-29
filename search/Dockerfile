FROM getmeili/meilisearch:v1.11.3@sha256:6f8f1fc29d1ff67b4d8d503bb4e0a69b55c8d42600decafde951344218193a27

COPY ./entrypoint.sh /entrypoint.sh

RUN chmod +x /entrypoint.sh

CMD [ "/entrypoint.sh" ]
