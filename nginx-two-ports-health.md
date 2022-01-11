# ...


- idea:
  - was written for ecs test and the heltcheck behavior of taskdefinitions where one container has two ports
  - configuring `DEBUG_FAIL_AFTER_TIME` should delete index.html after time so that port 8080 retruns 4xx error instead of 200  

```
docker run -t --rm -p 80:80 -p 8080:8080 --entrypoint=ash nginx:1.21.5-alpine -c \
  " \
  cp -R /usr/share/nginx/html /usr/share/nginx/html2; \
  echo done - copy html to html2; \
  echo ''; \
  cp /etc/nginx/conf.d/default.conf /etc/nginx/conf.d/e2nd.conf; \
  echo 'done - copy nginx server cfg'; \
  echo ''; \
  sed -i '/^    listen.*/c\\    listen       8080;' /etc/nginx/conf.d/e2nd.conf; \
  sed -i '/^\        root   \/usr\/share\/nginx\/html;.*/c\\        root   \/usr\/share\/nginx\/html2;' /etc/nginx/conf.d/e2nd.conf; \
  echo 'done - change nginx server cfg 2'; \
  echo ''; \
  sed -i 's|Welcome|No2 Welcome|g' /usr/share/nginx/html2/index.html; \
  echo 'done - change ondex.html no 2'; \
  echo ''; \
  (sleep ${DEBUG_FAIL_AFTER_TIME} && rm /usr/share/nginx/html2/index.html) & \
  nginx -g 'daemon off;'; \
  "
```

