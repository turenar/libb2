conf_file = configure_file(
	configuration: conf,
	output: 'config.h')

fat_libraries = []
if get_option('fat')
	foreach type : ['blake2b', 'blake2s']
		local_args = []
		foreach simd_type : ['ref', 'sse2', 'ssse3', 'sse4.1', 'avx', 'xop']
			if simd_type != 'ref'
				local_args += '-m' + simd_type
				source = type + '.c'
			else
				source = type + '-ref.c'
			endif
			# strip dot of 4.1
			suffix = ''.join(simd_type.split('.'))
			fat_libraries += static_library(type + '-' + simd_type,
				[source, conf_file],
				include_directories: include_directories('.'),
				c_args: ['-DSUFFIX=_' + suffix, local_args])
		endforeach
	endforeach
endif

b2_files_array = ['blake2sp.c', 'blake2bp.c']
if get_option('fat')
	b2_files_array += ['blake2-dispatch.c']
else
	b2_files_array += ['blake2s-ref.c', 'blake2b-ref.c']
endif
b2_files = files(b2_files_array)
