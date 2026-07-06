project('xserver', 'c',
        default_options: [
            'buildtype=debugoptimized',
            'c_std=gnu99',
            'warning_level=1',
        ],
        version: '25.2.0',
        meson_version: '>= 0.61.0',
)
release_date = '2026-06-21'

cc = meson.get_compiler('c')

add_project_arguments('-fno-strict-aliasing', language : 'c')
add_project_arguments('-fvisibility=hidden', language : 'c')
add_project_arguments('-fno-common', language: 'c')
add_project_arguments('-Wchar-subscripts', language: 'c')

# workaround for mesa bug causing GLsync to be typedef'ed multiple times
# this is breaking -Werror on FreeBSD
if host_machine.system() == 'freebsd'
    add_project_arguments('-Wno-error=typedef-redefinition', language: 'c')
endif

# workaround for mingw32 bug, which is demanding gnu_print attribute
# while not actually understanding it
if host_machine.system() == 'windows'
    add_project_arguments('-Wno-error=suggest-attribute=format', language: 'c')
endif

add_project_link_arguments('-fvisibility=hidden', language : 'c')

# global config data -- needs to be declared early
conf_data = configuration_data()
conf_data.set('_DIX_CONFIG_H_', '1')

if cc.get_id() == 'gcc' or cc.get_id() == 'clang'
    test_wflags = [
        '-Wchar-subscripts',
        '-Wpointer-arith',
        '-Wmissing-declarations',
        '-Wformat=2',
        '-Wstrict-prototypes',
        '-Wmissing-prototypes',
        '-Wnested-externs',
        '-Wbad-function-cast',
        '-Wold-style-definition',
        '-Wunused',
        '-Wuninitialized',
        '-Wshadow',
        '-Wmissing-noreturn',
        '-Wmissing-format-attribute',
        '-Wredundant-decls',
        '-Werror=implicit',
        '-Werror=nonnull',
        '-Werror=init-self',
        '-Werror=main',
        '-Werror=missing-braces',
        '-Werror=sequence-point',
        '-Werror=return-type',
        '-Werror=trigraphs',
        '-Werror=array-bounds',
        '-Werror=write-strings',
        '-Werror=address',
        '-Werror=int-to-pointer-cast',
        '-Werror=pointer-to-int-cast',
        '-Woverride-init',
        '-Wshift-negative-value',
        '-Wvla',
        '-Wincompatible-pointer-types',
    ]
else
    test_wflags = []
endif

common_wflags = []
foreach wflag: test_wflags
    if cc.has_argument(wflag)
        common_wflags += [ wflag ]
    endif
endforeach

add_project_arguments(common_wflags, language : ['c', 'objc'])

libdrm_req = '>= 2.4.89'
libselinux_req = '>= 2.0.86'
xext_req = '>= 1.0.99.4'
xproto_req = '>= 7.0.31'
gbm_req = '>= 10.2'
xf86dgaproto_req = '>= 2.0.99.1'
xshmfence_req = '>= 1.1'

wayland_req = '>= 1.3.0'
wayland_protocols_req = '>= 1.24'

x11_dep = dependency('x11')
xproto_dep = dependency('xproto', version: xproto_req, fallback: ['xorgproto', 'ext_xorgproto'])
randrproto_dep = dependency('randrproto', version: '>= 1.6.0', fallback: ['xorgproto', 'ext_xorgproto'])
renderproto_dep = dependency('renderproto', version: '>= 0.11', fallback: ['xorgproto', 'ext_xorgproto'])
xextproto_dep = dependency('xextproto', version: '>= 7.2.99.901', fallback: ['xorgproto', 'ext_xorgproto'])
inputproto_dep = dependency('inputproto', version: '>= 2.3.99.1', fallback: ['xorgproto', 'ext_xorgproto'])
kbproto_dep = dependency('kbproto', version: '>= 1.0.3', fallback: ['xorgproto', 'ext_xorgproto'])
fontsproto_dep = dependency('fontsproto', version: '>= 2.1.3', fallback: ['xorgproto', 'ext_xorgproto'])
fixesproto_dep = dependency('fixesproto', version: '>= 6.0', fallback: ['xorgproto', 'ext_xorgproto'])
damageproto_dep = dependency('damageproto', version: '>= 1.1', fallback: ['xorgproto', 'ext_xorgproto'])
xcmiscproto_dep = dependency('xcmiscproto', version: '>= 1.2.0', fallback: ['xorgproto', 'ext_xorgproto'])
bigreqsproto_dep = dependency('bigreqsproto', version: '>= 1.1.0', fallback: ['xorgproto', 'ext_xorgproto'])
presentproto_dep = dependency('presentproto', version: '>= 1.4', fallback: ['xorgproto', 'ext_xorgproto'])

videoproto_dep = dependency('videoproto', fallback: ['xorgproto', 'ext_xorgproto'])
compositeproto_dep = dependency('compositeproto', version: '>= 0.4', fallback: ['xorgproto', 'ext_xorgproto'])
recordproto_dep = dependency('recordproto', version: '>= 1.13.99.1', fallback: ['xorgproto', 'ext_xorgproto'])
scrnsaverproto_dep = dependency('scrnsaverproto', version: '>= 1.1', fallback: ['xorgproto', 'ext_xorgproto'])
resourceproto_dep = dependency('resourceproto', version: '>= 1.2.0', fallback: ['xorgproto', 'ext_xorgproto'])
xf86driproto_dep = dependency('xf86driproto', version: '>= 2.1.0', fallback: ['xorgproto', 'ext_xorgproto'], required: get_option('dri1') == 'true')
dri2proto_dep = dependency('dri2proto', version: '>= 2.8', fallback: ['xorgproto', 'ext_xorgproto'], required: get_option('dri2') == 'true')
dri3proto_dep = dependency('dri3proto', version: '>= 1.4', fallback: ['xorgproto', 'ext_xorgproto'], required: get_option('dri3') == 'true')
xineramaproto_dep = dependency('xineramaproto', fallback: ['xorgproto', 'ext_xorgproto'])
xf86bigfontproto_dep = dependency('xf86bigfontproto', version: '>= 1.2.0', fallback: ['xorgproto', 'ext_xorgproto'], required: get_option('xf86bigfont'))
xf86vidmodeproto_dep = dependency('xf86vidmodeproto', version: '>= 2.2.99.1', fallback: ['xorgproto', 'ext_xorgproto'])
applewmproto_dep = dependency('applewmproto', version: '>= 1.4', fallback: ['xorgproto', 'ext_xorgproto'], required: false)
xshmfence_dep = dependency('xshmfence', version: xshmfence_req, required: false)
dpmsproto_dep = dependency('dpmsproto', version: '>= 1.2', required: get_option('dpms'))
xwaylandproto_dep = dependency('xwayland-protocols', required: false)

pixman_dep = dependency('pixman-1')
libbsd_dep = dependency('libbsd-overlay', required: false)
xkbcomp_dep = dependency('xkbcomp', required: false)
xkbfile_dep = dependency('xkbfile')
xfont2_dep = dependency('xfont2', version: '>= 2.0')

dbus_required = get_option('systemd_logind') == 'true'
dbus_dep = dependency('dbus-1', version: '>= 1.0', required: dbus_required)

seat_libseat_required = get_option('seatd_libseat') == 'true'
seat_libseat_dep = dependency('libseat', version: '>= 0.9.1', required: seat_libseat_required)

build_systemd = get_option('systemd_notify') == 'true'
# libsystemd-daemon was moved into libsystemd in version 209
libsystemd_daemon_dep = dependency('libsystemd', version: '>= 209', required: false)
if not libsystemd_daemon_dep.found()
    libsystemd_daemon_dep = dependency('libsystemd-daemon', required: false)
endif

build_hashtable = false

# Resolve default values of some options
xkb_dir = get_option('xkb_dir')
if xkb_dir == ''
    if xkbcomp_dep.found() and xkbcomp_dep.type_name() == 'pkgconfig'
        xkb_dir = xkbcomp_dep.get_variable(pkgconfig : 'xkbconfigdir')
    endif
    if xkb_dir == ''
        xkb_dir = join_paths(get_option('prefix'), 'share/X11/xkb')
    endif
endif

xkb_output_dir = get_option('xkb_output_dir')
if xkb_output_dir == ''
    xkb_output_dir = join_paths(xkb_dir, 'compiled')
endif

xkb_bin_dir = get_option('xkb_bin_dir')
if xkb_bin_dir == ''
    if xkbcomp_dep.found() and xkbcomp_dep.type_name() == 'pkgconfig'
        xkb_bin_dir = xkbcomp_dep.get_variable(pkgconfig : 'bindir')
    endif
    if xkb_bin_dir == ''
        xkb_bin_dir = join_paths(get_option('prefix'), get_option('bindir'))
    endif
endif

dfp = get_option('default_font_path')
if dfp == ''
    fontrootdir = get_option('fontrootdir')
    if fontrootdir == ''
        fontutil_dep = dependency('fontutil', required: false)
        if fontutil_dep.found()
            fontrootdir = fontutil_dep.get_variable(pkgconfig : 'fontrootdir')
        else
            fontrootdir = join_paths(get_option('prefix'), get_option('datadir'), 'fonts', 'X11')
        endif
    endif
    dfp_elements = [
        join_paths(fontrootdir, 'misc'),
        join_paths(fontrootdir, 'TTF'),
        join_paths(fontrootdir, 'OTF'),
        join_paths(fontrootdir, 'Type1'),
        join_paths(fontrootdir, '100dpi'),
        join_paths(fontrootdir, '75dpi'),
    ]
    if host_machine.system() == 'darwin'
       dfp_elements += [
       '/Library/Fonts',
       '/System/Library/Fonts',
    ]
    endif
    default_font_path = ','.join(dfp_elements)
else
    default_font_path = dfp
endif

glamor_option = get_option('glamor')

hal_option = get_option('hal')
if ['windows', 'darwin', 'cygwin'].contains(host_machine.system())
    hal_option = 'false'
endif

udev_option = get_option('udev')
udev_kms_option = get_option('udev_kms')
if not dependency('libudev', required: (udev_option == 'true' or udev_kms_option == 'true')).found()
    udev_option = 'false'
    udev_kms_option = 'false'
endif

build_udev = (udev_option != 'false')
build_udev_kms = (udev_kms_option != 'false')

if get_option('seatd_libseat') == 'auto'
    build_seatd_libseat = build_udev_kms and seat_libseat_dep.found()
else
    build_seatd_libseat = get_option('seatd_libseat') == 'true'
endif


if get_option('systemd_logind') == 'auto'
    build_systemd_logind = build_udev_kms and dbus_dep.found() and not build_seatd_libseat
else
    build_systemd_logind = get_option('systemd_logind') == 'true'
endif

if build_systemd_logind and build_seatd_libseat
    error('Support for systemd-logind and seatd-libseat are mutually exclusive: choose either.')
endif

with_dtrace = get_option('dtrace')
if with_dtrace
    dtrace = find_program('dtrace', required: true)
endif

build_xorg_sdk = get_option('xorg-sdk')

build_xorg = false
if (host_machine.system() != 'windows')
    if get_option('xorg') == 'auto'
        build_xorg = (host_machine.system() != 'darwin' and
                      host_machine.system() != 'windows')
    else
        build_xorg = get_option('xorg') == 'true'
    endif
endif

if build_xorg
    build_xorg_sdk = true
    libxcvt_dep = dependency('libxcvt', fallback: ['libxcvt', 'libxcvt_dep'], required: build_xorg)
endif

xorgsdkdir = join_paths(get_option('prefix'), get_option('includedir'), 'xorg')

## configure Xnest - nesting X server
build_xnest = get_option('xnest') != 'false'

build_xwin = false
if get_option('xwin') == 'auto'
    if (host_machine.system() == 'cygwin' or
        host_machine.system() == 'windows')
            build_xwin = true
    endif
else
    build_xwin = get_option('xwin') == 'true'
endif

build_xquartz = false
if get_option('xquartz') == 'auto'
    if host_machine.system() == 'darwin'
            build_xquartz = true
    endif
else
    build_xquartz = get_option('xquartz') == 'true'
endif

build_rootless = false
if build_xquartz
    build_rootless = true
endif

build_xvfb = get_option('xvfb')

build_xephyr = get_option('xephyr')

build_xfbdev = get_option('xfbdev')

## configure Xwayland - X Server for Wayland
build_xwayland = false
if (host_machine.system() != 'darwin' and
    host_machine.system() != 'windows' and
    get_option('xwayland') != 'false')
    xwayland_required = get_option('xwayland') == 'true'
    
    xwayland_path = get_option('xwayland-path')
    if (xwayland_path == '')
        xwayland_path = join_paths(get_option('prefix'), get_option('bindir'))
    endif

    xwayland_dep = [
        dependency('wayland-client', version: wayland_req, required: xwayland_required),
        dependency('wayland-protocols', version: wayland_protocols_req, required: xwayland_required),
        dependency('libxcvt', fallback: ['libxcvt', 'libxcvt_dep'], required: xwayland_required),
    ]
    
    if build_glamor
        xwayland_dep += dependency('xshmfence', version: xshmfence_req, required: xwayland_required)
        xwayland_dep += gbm_dep
        xwayland_dep += epoxy_dep
    endif

    # Check for libei (input emulation)
    libei_dep = dependency('libei-1.0', required: false)
    liboeffis_dep = dependency('liboeffis-0', required: false)
    build_ei = libei_dep.found()
    build_ei_portal = build_ei and liboeffis_dep.found()
    
    if build_ei
        xwayland_dep += libei_dep
        if build_ei_portal
            xwayland_dep += liboeffis_dep
        endif
    endif

    # Check for libdecor (window decorations)
    libdecor_dep = dependency('libdecor-0', required: false)
    have_libdecor = libdecor_dep.found()
    if have_libdecor
        xwayland_dep += libdecor_dep
    endif

    build_xwayland = true
    build_xwayland_glamor = build_glamor
    
    foreach d: xwayland_dep
        if not d.found()
            build_xwayland = false
        endif
    endforeach
endif

build_xserver = (build_xorg or
                 build_xnest or
                 build_xvfb or
                 build_xquartz or
                 build_xephyr or
                 build_xfbdev or
                 build_xwayland)

summary({
        'Xorg': build_xorg,
        'Xnest': build_xnest,
        'Xvfb': build_xvfb,
        'Xwin': build_xwin,
        'Xquartz': build_xquartz,
        'Xephyr': build_xephyr,
        'Xfbdev': build_xfbdev,
        'Xwayland': build_xwayland,
    },
    section: 'DDX',
    bool_yn: true,
)
