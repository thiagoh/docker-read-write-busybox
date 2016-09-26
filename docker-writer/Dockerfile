FROM busybox

RUN mkdir /my-data && \
	cd my-data && \
	touch readable-file && \
	echo "this string is present in readable-file file $(date)" >> readable-file

VOLUME /my-data
