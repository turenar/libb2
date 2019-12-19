project('libb2', 'c',
	version: '0.98.1',
	default_options: ['c_std=gnu99'],
	meson_version: '>=0.52.0')
library_version = '1.4.0'

c_compiler = meson.get_compiler('c')

conf = configuration_data()

if get_option('fat')
	simd_flags = ['sse2', 'ssse3', 'sse4.1', 'avx', 'xop']
	foreach simd_flag : simd_flags
		supported = c_compiler.has_argument('-m' + simd_flag)
		if not supported and get_option('fat')
			error('Compiler does not know -m' + simd_flag)
		endif
	endforeach
endif

functions = ['explicit_bzero', 'explicit_memset', 'memset_s', 'memset']
foreach function : functions
	supported = c_compiler.has_function(function)
	if supported
		conf.set('HAVE_' + function.to_upper(), 1)
	endif
endforeach

if host_machine.endian() == 'little'
	conf.set('NATIVE_LITTLE_ENDIAN', 1)
endif

openmp_dep = dependency('openmp', required: false)

subdir('src')


b2_lib = library('b2', b2_files,
	c_args: ['-DSUFFIX='],
	link_with: fat_libraries,
	include_directories: include_directories('src'),
	dependencies: [openmp_dep],
	soversion: library_version)

b2_dep = declare_dependency(
	link_with: b2_lib,
	include_directories: include_directories('src', is_system: true),
	dependencies: [openmp_dep])

if not meson.is_subproject()
	subsets = ['s', 'b', 'sp', 'bp']
	foreach subset : subsets
		test_exe = executable('blake2' + subset + '-test',
			['src/blake2' + subset + '-test.c'],
			dependencies: b2_dep)
		test('blake2' + subset + '-test', test_exe)
	endforeach
endif
