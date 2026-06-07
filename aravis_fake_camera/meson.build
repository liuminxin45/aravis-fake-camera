project ('aravis', 'c', 'cpp', version: '0.9.3', meson_version: '>=0.57.0')

gnome = import('gnome')
pkg = import ('pkgconfig')

aravis_version = meson.project_version ()
aravis_api_version = '0.10'

versions = aravis_version.split ('.')
aravis_major_version = versions[0].to_int ()
aravis_minor_version = versions[1].to_int ()
aravis_micro_version = versions[2].to_int ()

aravis_data_dir = get_option ('datadir') / 'aravis-@0@'.format (aravis_api_version)

cc = meson.get_compiler ('c')
cxx = meson.get_compiler ('cpp')

if cc.get_id()=='gcc' or cc.get_id()=='clang'
  warning_c_args = [
    '-Wduplicated-branches',
    '-Wimplicit-fallthrough',
    '-Wmisleading-indentation',
    '-Wstrict-prototypes',
    '-Wunused',
    '-Wunused-variable',
    '-Wdeclaration-after-statement',
    '-Wformat=2',
    '-Wimplicit-function-declaration',
    '-Winit-self',
    '-Wmissing-include-dirs',
    '-Wmissing-prototypes',
    '-Wpointer-arith',
    '-Wformat-nonliteral',
    '-Wenum-conversion',
    '-Wmaybe-uninitialized'
    ]

  add_project_arguments(cc.get_supported_arguments(warning_c_args), language: 'c')
elif cc.get_id()=='msvc'
  warning_c_args = [
    '/wd4244', # Disable int-float implicit casting warnings
    '/wd4090', # Disable const qualifiers warnings
    '/utf-8'
  ]

  add_project_arguments(cc.get_supported_arguments(warning_c_args), language: 'c')
endif

build_shared = get_option('default_library') == 'shared'

if cc.get_id() == 'msvc'
  if build_shared
    cc_export_define = '__declspec(dllexport) extern'
  else
    cc_export_define = 'extern'  # static build: no export
  endif
elif cc.has_argument('-fvisibility=hidden')
  if build_shared
    add_project_arguments('-fvisibility=hidden', language: 'c')
    cc_export_define = 'extern __attribute__ ((visibility ("default")))'
  else
    cc_export_define = 'extern'
  endif
else
  cc_export_define = 'extern'
endif

glib_dep = dependency ('glib-2.0', version: '>=2.58', required: true)
gobject_dep = dependency ('gobject-2.0', required: true)
gio_dep = dependency ('gio-2.0', required: true)
xml2_dep = dependency ('libxml-2.0', required: true)
libz_dep = dependency ('zlib', required: true)
gmodule_dep = dependency ('gmodule-2.0', required: true)

aravis_public_dependencies = [glib_dep, gobject_dep, gio_dep]
aravis_dependencies = [aravis_public_dependencies, xml2_dep, libz_dep, gmodule_dep]
if cc.get_id()!='msvc'
	libm = cc.find_library ('m', required: true)
	aravis_dependencies += libm
endif

if host_machine.system()=='windows'
	aravis_dependencies += [
		cc.find_library('ws2_32', required: true),  # socket functions
		cc.find_library('iphlpapi', required: true) # GetAdaptersAddresses
	]
endif

packet_socket_option = get_option('packet-socket')
if host_machine.system()=='linux'
	has_if_packet = cc.has_header ('linux' /'if_packet.h')
	if packet_socket_option.enabled()
		if not has_if_packet
			error ('missing header for packet-socket support')
		endif
		packet_socket_enabled = true
	else
		packet_socket_enabled = has_if_packet and packet_socket_option.auto()
	endif
else # not Linux
	if packet_socket_option.enabled()
		warning('packet-socket option ignored on non-Linux')
	endif
	packet_socket_enabled = false
endif

subdir ('src')

meson.override_dependency ('aravis-' + aravis_api_version, aravis_library_dependencies)

summary (
  {
  'prefix': get_option('prefix'),
  'bindir': get_option('bindir'),
  'libdir': get_option('libdir'),
  'datadir': get_option('datadir'),
  },
  section: 'Directories'
)

summary (
  {
  'Fake GigE Vision camera': true,
  },
  section: 'Options'
)
