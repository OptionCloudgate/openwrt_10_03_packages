# Appinc Package:

With this package it is possible to bundle an already built cloudgate app with your own application.
This is a lazy approach to combine images, or combine applications you don't have the source of.
There are a few things to keep in mind though.

1) **base image conflict:**
   Since application are linked to specific library versions all included app images
   should be from a compatible version (linked to the ~same FW/SDK version as you are building).
   Most of the time there are no breaking changes so you should be fine ...
2) **image conflicts:**
   it is possible that multiple images (or your own app) include the same package but compiled
   differently. E.g. assume app A compiled netcat with option X because app A required it. app B also
   compiled netcat but without option X. If you use appinc to combine app A and app B only one of the netcat
   binaries will be included, which will break app A or app B. The appinc package cannot help you with this conflict
3) **config conflicts:**
   It is possible to store config files (uci) in the application. These are located in 
   $(TOPDIR)/config. Again, it is possible the included images adjust the same (uci) config file, causing
   a conflict. The appinc package will detect this issue, search for CFGCONFLICT to see how to resolve this.
4) **startup file:**
   Each app images will have its own startup entry point. Most of the time this is the "isv" file in the root.
   The appinc package will rename these "isv" files to "isv-appimagename" and you are responsible to call all
   of these in your own "isv" file (else the other images will not start)"


# Basic Usage:

- From an SDK where you are able to build your own image,
- Enabled the appinc package in your config (make menuconfig)
  If not yet available you can get it with ./scripts/feeds update && ./scripts/feeds install appinc
- Copy the image files you want to include to a place reachable from the root of the SDK
- Set the list of images to include (make menuconfig, option of the appinc package)
- adjust your own startup file (isv?) to call each of the included apps isv files.
  the isv files of the included images will have been renamed to isv-appinc-<imagename>
  example: "for APP in /rom/mnt/cust/isv-appinc*; do echo ($APP &); done"
- make
- in case of a config error see CFGCONFLICT to resolve it


# CFGCONFLICT:

The included app(s) can try to modify the same config.
In case there is a conflict:
- manually merge the conflicting config files
- put the result in $(TOPDIR)/config/config/<theconfigfile>

You can find the config files from each application you tried to include at the following ~location (versions might be different)

> $(TOPDIR)/build_dir/target*/appinc*/apps/myincludedapp/squashfs-config-full/config

> example: build_dir/target-arm_v5te_uClibc-0.9.33.2_eabi/appinc-0.2/apps/option_luvitred_sras_v2_27_0_signed/squashfs-config-full/config

you can use the version from each of the apps + your own version to create a final merge version,
which need to be placed in $(TOPDIR)/config/config.
- Next, add this particular config to the list of configs to ignore (make menuconfig, option of the appinc package)
  so the configs are not copied from the included images but only taken from your source.
  ps, this item is a list so you can e.g. set "devices m2mweb" so it will ignore the "devices" and "m2mweb" config file

