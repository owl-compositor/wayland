test_runner_deps = [
	cc.find_library('dl', required: false),
	dependency('threads'),
	ffi_dep,
	wayland_util_dep,
	wayland_private_dep,
	wayland_client_dep,
	wayland_server_dep,
]

test_runner_sources = [
	'test-runner.c',
	'test-helpers.c',
	'test-compositor.c',
]

test_runner = static_library(
	'test-runner',
	sources: test_runner_sources,
	include_directories: [ root_inc, src_inc ],
	dependencies: test_runner_deps,
)

test_runner_dep = declare_dependency(
	link_with: test_runner,
	include_directories: [ src_inc ],
	dependencies: [
		dependency('threads'),
		cc.find_library('dl', required: false)
	]
)

tests_protocol_xml = files('../protocol/tests.xml')

tests_server_protocol_h = custom_target(
	'test server protocol header',
	command: [ wayland_scanner_for_build, 'server-header', '@INPUT@', '@OUTPUT@' ],
	input: tests_protocol_xml,
	output: 'tests-server-protocol.h'
)

tests_client_protocol_c = custom_target(
	'test client protocol header',
	command: [ wayland_scanner_for_build, 'client-header', '@INPUT@', '@OUTPUT@' ],
	input: tests_protocol_xml,
	output: 'tests-client-protocol.h'
)

tests_protocol_c = custom_target(
	'test protocol source',
	command: [ wayland_scanner_for_build, 'public-code', '@INPUT@', '@OUTPUT@' ],
	input: tests_protocol_xml,
	output: 'tests-protocol.c'
)

benchmark(
	'fixed-benchmark',
	executable(
		'fixed-benchmark',
		'fixed-benchmark.c',
		dependencies: test_runner_dep
	)
)

executable(
	'exec-fd-leak-checker',
	'exec-fd-leak-checker.c',
	dependencies: test_runner_dep
)

test(
	'cpp-compile-test',
	executable(
		'cpp-compile-test',
		'cpp-compile-test.cpp',
		wayland_server_protocol_core_h,
		include_directories: src_inc
	)
)

sed_path = find_program('sed').path()

test(
	'scanner-test',
	find_program('scanner-test.sh'),
	env: [
		'TEST_DATA_DIR=@0@/data'.format(meson.current_source_dir()),
		'TEST_OUTPUT_DIR=@0@/output'.format(meson.current_build_dir()),
		'SED=@0@'.format(sed_path),
		'WAYLAND_SCANNER=@0@'.format(wayland_scanner.full_path()),
	],
)

# test name, extra sources, extra dependencies
tests = [
	[ 'array-test' ],
	[
		'client-test', [
			wayland_server_protocol_h,
		]
	],
	[
		'display-test', [
			tests_server_protocol_h,
			tests_client_protocol_c,
			tests_protocol_c,
		],
	],
	[
		'connection-test', [
			wayland_server_protocol_h,
		]
	],
	[
		'event-loop-test', [
			wayland_server_protocol_h,
		]
	],
	[ 'fixed-test' ],
	[
		'interface-test', [
			wayland_client_protocol_h,
		]
	],
	[ 'list-test' ],
	[ 'map-test' ],
	[
		'sanity-test', [
			wayland_server_protocol_h,
		]
	],
	[
		'socket-test', [
			wayland_client_protocol_h,
			wayland_server_protocol_h,
		]
	],
	[
		'queue-test', [
			wayland_client_protocol_h,
			wayland_server_protocol_h,
		]
	],
	[
		'signal-test', [
			wayland_server_protocol_h,
		]
	],
	[
		'newsignal-test', [
			files('../src/wayland-server.c'),
			wayland_server_protocol_h,
		],
	],
	[
		'resources-test', [
			wayland_server_protocol_core_h,
		],
	],
	[
		'message-test', [
			wayland_client_protocol_h,
			wayland_server_protocol_h,
		]
	],
	[	'compositor-introspection-test', [
			wayland_client_protocol_h,
			wayland_server_protocol_h,
		]
	],
	[
		'protocol-logger-test', [
			wayland_server_protocol_core_h,
			wayland_client_protocol_core_h,
		],
	],
	[
		'headers-test', [
			'headers-protocol-test.c',
			'headers-protocol-core-test.c',
			wayland_server_protocol_core_h,
			wayland_client_protocol_core_h,
		],
	],
	[ 'os-wrappers-test' ],
]

foreach t: tests
	t_name = t.get(0)
	t_sources = [ t_name + '.c' ] + t.get(1, [])
	t_deps = [test_runner_dep] + t.get(2, [])
	bin = executable(t_name, t_sources, dependencies: t_deps)
	test(
		t_name,
		bin,
		env: [
			'TEST_SRC_DIR=@0@'.format(meson.current_source_dir()),
			'TEST_BUILD_DIR=@0@'.format(meson.current_build_dir()),
		],
	)
endforeach
