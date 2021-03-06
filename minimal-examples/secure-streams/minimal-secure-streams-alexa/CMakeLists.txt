cmake_minimum_required(VERSION 2.8)
include(CheckCSourceCompiles)
include(LwsCheckRequirements)

set(SAMP lws-minimal-secure-streams-alexa)
set(SRCS main.c alexa.c audio.c)

set(requirements 1)
require_lws_config(LWS_ROLE_H1 1 requirements)
require_lws_config(LWS_WITHOUT_CLIENT 0 requirements)
require_lws_config(LWS_WITH_SECURE_STREAMS 1 requirements)
require_lws_config(LWS_WITH_SECURE_STREAMS_STATIC_POLICY_ONLY 0 requirements)
require_lws_config(LWS_WITH_ALSA 1 requirements)

if (requirements)
	add_executable(${SAMP} ${SRCS})

	if (websockets_shared)
		target_link_libraries(${SAMP} websockets_shared asound pv_porcupine mpg123)
		add_dependencies(${SAMP} websockets_shared)
	else()
		target_link_libraries(${SAMP} websockets asound pv_porcupine mpg123)
	endif()

	if (LWS_WITH_SECURE_STREAMS_PROXY_API)	
		add_compile_options(-DLWS_SS_USE_SSPC)

		add_executable(${SAMP}-client ${SRCS})
		if (websockets_shared)
			target_link_libraries(${SAMP}-client websockets_shared asound pv_porcupine mpg123)
			add_dependencies(${SAMP}-client websockets_shared)
		else()
			target_link_libraries(${SAMP}-client websockets asound pv_porcupine mpg123)
		endif()
	endif()
	
endif()
