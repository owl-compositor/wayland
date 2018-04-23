# man section, [ [ xml_file, name, alias ] ]
man_pages = [
	[
		'3', [
			[ 'wl_display_connect.xml', 'wl_display_connect', 'wl_display_connect_to_fd' ],
		],
	]
]

xsltproc_opts = [
	'--nonet',
	'--stringparam', 'man.authors.section.enabled', '0',
	'--stringparam', 'man.copyright.section.enabled', '0',
	'--stringparam', 'funcsynopsis.style', 'ansi',
	'--stringparam', 'man.output.quietly', '1',
]

foreach section: man_pages
	section_number = section[0]
	pages = section.get(1, [])

	foreach page: pages
		xml_input = page[0]
		name = page[1]
		alias = page.get(2, '')

		man_output = name + '.' + section_number
		if alias != ''
			alias_output = alias + '.' + section_number
		else
			alias_output = []
		endif

		man_page = custom_target(
			name + '-man',
			command: [
				xsltproc,
				xsltproc_opts,
				'-o', '@OUTPUT0@',
				manpage_xsl,
				'@INPUT@',
			],
			input: xml_input,
			output: [ man_output, alias_output ],
			install: true,
			install_dir: join_paths(get_option('prefix'), get_option('mandir'), 'man' + section_number),
			build_by_default: true,
		)
	endforeach
endforeach
