subdir('stop-words')

enums = gnome.mkenums('tracker-enum-types',
  sources: 'tracker-enums.h',
  c_template: 'tracker-enum-types.c.template',
  h_template: 'tracker-enum-types.h.template',
)

tracker_common_enum_header = enums[1]

tracker_common_sources = [
  'tracker-date-time.c',
  'tracker-dbus.c',
  'tracker-domain-ontology.c',
  'tracker-file-utils.c',
  'tracker-ioprio.c',
  'tracker-log.c',
  'tracker-sched.c',
  'tracker-type-utils.c',
  'tracker-utils.c',
  'tracker-locale.c',
  'tracker-parser-utils.c',
  'tracker-language.c',
  enums[0], enums[1],
]

if unicode_library_name == 'icu'
  tracker_common_sources += 'tracker-parser-libicu.c'
else
  tracker_common_sources += 'tracker-parser-libunistring.c'
endif

# FIXME: need to link against -lkvm on OpenBSD, see configure.ac
tracker_common_dependencies = [glib, gio, gio_unix, libmath]

if have_libstemmer
  tracker_common_dependencies += [libstemmer]
endif

libtracker_common = library('tracker-common',
  tracker_common_sources,
  dependencies: tracker_common_dependencies + [unicode_library],
  c_args: tracker_c_args,
  include_directories: [configinc, srcinc],
  install: true,
  install_dir: tracker_internal_libs_dir,
)

commoninc = include_directories('.')

# This dependency can't be used everywhere right now. The issue is that if
# every other library depends on this one, we end up with a combinatorial
# explosion that results in the link line for src/tracker/tracker being
# nearly 200,000 characters long which causes this kind of error:
#
#     Linking target src/tracker/tracker
#     ninja: fatal: posix_spawn: Argument list too long
#
# Upstream issue: https://github.com/mesonbuild/meson/issues/671
tracker_common_dep = declare_dependency(
  sources: [tracker_common_enum_header],
  link_with: libtracker_common,
  dependencies: tracker_common_dependencies,
  include_directories: [configinc, srcinc, commoninc],
)
