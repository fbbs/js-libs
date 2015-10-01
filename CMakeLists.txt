cmake_minimum_required(VERSION 2.8)

project(fbbs-js-libs)

find_program(UGLIFYJS uglifyjs)

set(DOCUMENT_ROOT "/var/www/localhost/htdocs/fbbs")

function(publish_js MINIFIED SCRIPTS)
	set(OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/${MINIFIED}.min.js)
	if(UGLIFYJS)
		set(OUTPUT ${OUTPUT} ${CMAKE_CURRENT_BINARY_DIR}/${MINIFIED}.map.js)
		add_custom_command(OUTPUT ${OUTPUT}
				COMMAND cd ${CMAKE_CURRENT_SOURCE_DIR}
						&& cp ${SCRIPTS} ${CMAKE_CURRENT_BINARY_DIR}
				COMMAND ${UGLIFYJS} ${SCRIPTS} -c -m -o ${MINIFIED}.min.js
						--source-map ${MINIFIED}.map.js -p relative
				DEPENDS ${SCRIPTS})
	else(UGLIFYJS)
		add_custom_command(OUTPUT ${OUTPUT}
				COMMAND cd ${CMAKE_CURRENT_SOURCE_DIR} && cat ${SCRIPTS} > ${OUTPUT}
				DEPENDS ${SCRIPTS})
	endif(UGLIFYJS)
	add_custom_target(${MINIFIED} ALL DEPENDS ${OUTPUT})
	install(FILES ${OUTPUT} ${SCRIPTS} DESTINATION ${DOCUMENT_ROOT}/js)
endfunction(publish_js)

set(SCRIPTS
	json2-20150928.js
	store-1.3.17.20150928.js
	history.adapter.jquery-1.8b2.20150928.js
	history-1.8b2.20150928.js
	history.html4-1.8b2.20150928.js
	mustache-2.1.3.js)
publish_js(lib-20150928 "${SCRIPTS}")

set(SCRIPTS
	jquery-1.11.3.js)
publish_js(jquery-1.11.3 "${SCRIPTS}")
