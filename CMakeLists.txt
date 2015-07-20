cmake_minimum_required(VERSION 3.1)
project(gstreamer)

add_library(gstreamer SHARED
        win32/common/dirent.c
        win32/common/gstenumtypes.c
        gst/gst.c
        gst/gstobject.c
        gst/gstallocator.c
        gst/gstbin.c
        gst/gstbuffer.c
        gst/gstbufferlist.c
        gst/gstbufferpool.c
        gst/gstbus.c
        gst/gstcaps.c
        gst/gstcapsfeatures.c
        gst/gstchildproxy.c
        gst/gstclock.c
        gst/gstclock-linreg.c
        gst/gstcontext.c
        gst/gstcontrolbinding.c
        gst/gstcontrolsource.c
        gst/gstdatetime.c
        gst/gstdebugutils.c
        gst/gstdevice.c
        gst/gstdeviceprovider.c
        gst/gstdeviceproviderfactory.c
        gst/gstelement.c
        gst/gstelementfactory.c
        gst/gsterror.c
        gst/gstevent.c
        gst/gstformat.c
        gst/gstghostpad.c
        gst/gstdevicemonitor.c
        gst/gstinfo.c
        gst/gstiterator.c
        gst/gstatomicqueue.c
        gst/gstmessage.c
        gst/gstmeta.c
        gst/gstmemory.c
        gst/gstminiobject.c
        gst/gstpad.c
        gst/gstpadtemplate.c
        gst/gstparamspecs.c
        gst/gstpipeline.c
        gst/gstplugin.c
        gst/gstpluginfeature.c
        gst/gstpluginloader.c
        gst/gstpoll.c
        gst/gstpreset.c
        gst/gstquark.c
        gst/gstquery.c
        gst/gstregistry.c
        gst/gstregistrychunks.c
        gst/gstsample.c
        gst/gstsegment.c
        gst/gststructure.c
        gst/gstsystemclock.c
        gst/gsttaglist.c
        gst/gsttagsetter.c
        gst/gsttask.c
        gst/gsttaskpool.c
        gst/gsttoc.c
        gst/gsttocsetter.c
        gst/gsttypefind.c
        gst/gsttypefindfactory.c
        gst/gsturi.c
        gst/gstutils.c
        gst/gstvalue.c
        gst/gstparse.c
        gst/gstregistrybinary.c
        gst/gstregistry.c
#        gst/gsttrace.c

        win32/common/libgstreamer.def
)

target_include_directories(gstreamer PUBLIC
    ${DEPENDENCIES}/include
    ${GLIB_DIR}
    ${GLIB_DIR}/glib
    ${GLIB_DIR}/gmodule
    .
    gst
    win32/common
    ${CMAKE_BINARY_DIR}
)

target_compile_definitions(gstreamer
PUBLIC
    -DHAVE_CONFIG_H
    -DWIN32
    -DDISABLE_ORC
    -D_WINDOWS
    -DHAVE_WIN32
    -DGST_DISABLE_XML
    -DHAVE_SINH
    -DHAVE_COSH
    -DGST_DISABLE_GST_DEBUG
    -DGST_DISABLE_PARSE
PRIVATE
    -DGST_EXPORTS
)

find_library(GLIB_LIBRARY glib ${DEPENDENCIES}/lib)
find_library(GOBJECT_LIBRARY gobject ${DEPENDENCIES}/lib)
find_library(GMODULE_LIBRARY gmodule ${DEPENDENCIES}/lib)

target_link_libraries(gstreamer PUBLIC
    ${GLIB_LIBRARY}
    ${GOBJECT_LIBRARY}
    ${GMODULE_LIBRARY}
    ws2_32
)


file(COPY win32/common/gstconfig.h DESTINATION ${CMAKE_BINARY_DIR}/gst)
file(COPY win32/common/gstenumtypes.h DESTINATION ${CMAKE_BINARY_DIR}/gst)
file(COPY win32/common/gstversion.h DESTINATION ${CMAKE_BINARY_DIR}/gst)
file(COPY win32/common/config.h DESTINATION ${CMAKE_BINARY_DIR})

add_library(gstbase SHARED
    libs/gst/base/gstadapter.c
    libs/gst/base/gstbaseparse.c
    libs/gst/base/gstbasesink.c
    libs/gst/base/gstbasesrc.c
    libs/gst/base/gstbasetransform.c
    libs/gst/base/gstbitreader.c
    libs/gst/base/gstbytereader.c
    libs/gst/base/gstbytewriter.c
    libs/gst/base/gstcollectpads.c
    libs/gst/base/gstdataqueue.c
    libs/gst/base/gstflowcombiner.c
    libs/gst/base/gstpushsrc.c
    libs/gst/base/gstqueuearray.c
    libs/gst/base/gsttypefindhelper.c

    win32/common/libgstbase.def
)

target_include_directories(gstbase PRIVATE
    libs
)

target_link_libraries(gstbase PUBLIC
    gstreamer
)

add_library(gstcoreelements SHARED
    plugins/elements/gstcapsfilter.c
    plugins/elements/gstconcat.c
    plugins/elements/gstdownloadbuffer.c
    plugins/elements/gstelements.c
    plugins/elements/gstelements_private.c
    plugins/elements/gstfakesrc.c
    plugins/elements/gstfakesink.c
    plugins/elements/gstfdsrc.c
    plugins/elements/gstfdsink.c
    plugins/elements/gstfilesink.c
    plugins/elements/gstfilesrc.c
    plugins/elements/gstfunnel.c
    plugins/elements/gstidentity.c
    plugins/elements/gstinputselector.c
    plugins/elements/gstoutputselector.c
    plugins/elements/gstmultiqueue.c
    plugins/elements/gstqueue.c
    plugins/elements/gstqueue2.c
    plugins/elements/gstsparsefile.c
    plugins/elements/gsttee.c
    plugins/elements/gsttypefindelement.c
    plugins/elements/gststreamiddemux.c
    plugins/elements/gstvalve.c
)

target_include_directories(gstcoreelements PRIVATE
    libs
)

target_link_libraries(gstcoreelements PUBLIC
    gstreamer
    gstbase
)

install(TARGETS gstreamer gstbase
    RUNTIME DESTINATION bin
)

install(TARGETS gstcoreelements
    RUNTIME DESTINATION bin/plugins
)

# TODO:
#    gst-plugins-base/gst-libs/gst/app      libgstapp
#    gst-plugins-base/gst-libs/gst/video    libgstvideo
#    gst-plugins-base/app            libgstapp
#    gst-plugins-base/videoconvert   libgstvideoconvert
#    gst-plugins-base/videoscale     libgstvideoscale
#
#    gst-plugins-bad/autoconvert     libgstautoconvert
#
#    gst-plugins-good/ext/jpeg       libgstjpeg
#    gst-plugins-good/videobox       libgstvideobox
#    gst-plugins-good/videofilter    libgstvideofilter
