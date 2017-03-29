# [Trouble-shooting]Black screen after Ubuntu upgrade

### After upgrade Ubuntu to a new release, system might not boot normally, and counld not log into grub interface. Here is the solution:

  1. prepare a Ubuntu boot disk (USB, CD), using UtralISO or other tools.
  2. boot the computer with boot disk, log into a live session without installing.
  3. connect to the Internet
  4. Ctr+Alt+T open the termial, run following commands:

  ``` sh
  sudo add-apt-repository ppa:yannubuntu/boot-repair
  sudo apt-get update
  sudo apt-get install -y boot-repair
  boot-repair
  ```

  5. Then "Recommended repair" will do the job.
  6. Restart, boot with ubuntu partition, and have fun.
