---
title: 'yocto workflow'
layout: post
tags:
  - Yocto
category: yocto
---
#### yocto workflow
![](https://github.com/kchhero/kchhero.github.io/blob/master/assets/ext_images/yocto_images/recipe-workflow.png?raw=true)

    
---

#### recipe sequence
![](https://github.com/kchhero/kchhero.github.io/blob/master/assets/ext_images/yocto_images/sequence.jpg?raw=true)
    

---

#### kernel task sequence
do_fetch  >  do_unpack   >  do_qa_unpack  >  do_kernel_checkout  >  do_validate_branches  >  do_kernel_metadata  >
  do_patch  >  do_kernel_configme  >  sysroot_cleansstate  >  do_configure  >  do_qa_configure  >  do_kernel_configcheck  >
  do_compile  >  do_shared_workdir  >  do_compile_kernelmodules   ...

---

#### get task sequence
 bitbake -c listtasks <recipe_name>

---

#### Image generation
![](http://www.yoctoproject.org/docs/latest/ref-manual/figures/image-generation.png?raw=true)
