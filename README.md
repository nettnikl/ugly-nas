![name](ugly.png)

## DIY 8-disks NAS based on Odroid H3+ under $800 with disks

### Why is it ugly?

The early case versions of Ugly NAS are really [ugly](/ugly) (not mine, but looks similar) and I'm using it still. Check the original repo for info how it can look if you decide to use the original PSU and create a nice enclosure. My SSDs use too much power for the Odroid to handle, so i need a proper PSU.

### Cost (Jan 2025):

| Part  | EUR  |
|-------|--------:|
| [Odroid H3+](https://www.hardkernel.com/shop/odroid-h3-plus/) | 160.00 |
| [64GB eMMC Module](https://www.hardkernel.com/shop/64gb-emmc-module-h2/) | 28.00 |
| [Samsung 32GB DDR4](https://www.hardkernel.com/shop/samsung-32gb-ddr4-pc4-25600-so-dimm/) | 96.00 |
| [300W Power Supply Unit](https://www.reichelt.de/de/de/shop/produkt/psu_argus_tfx-300w-268757?PROVID=2378) | 37.50 |
| [Case Type 5](https://www.hardkernel.com/shop/odroid-h3-case-type-5/) | 20.00 |
| Shipping from hardkernel.com to Germany | 19.49 |
| 5 x [Samsung_SSD_870_QVO_4TB drives](https://www.mindfactory.de/product_info.php/4TB-Samsung-SSD-870-QVO-2-5Zoll--6-4cm--SATA-6Gb-s-3D-NAND-QLC--MZ-77Q4_1370758.html) | 5x ~290.00 |
| [6-bays enclosure](https://aliexpress.com/item/32921898033.html) | 48.29 |
| [PCI-E SATA extender for 6 ports](https://www.amazon.de/dp/B0BWHDPZ7D) | 28.00 |
| 6 x [Profiled SATA cables](https://www.amazon.de/dp/B0053JNMKI) | 18.00 |
| [SATA To DC 5.5mm/2.1mm](https://www.aliexpress.com/item/1005005425212413.html) | 3.00 |
| [M3 Spacers](https://www.aliexpress.com/item/1005005816515964.html) | 6.60 |
| **Sum** | **1914.88** | 

Beware that I spent much more on experimenting with different setups. The final cost above is after refining the best solution.

If you opt for a self-designed [case](/case), purchasing a fan and supports separately is necessary.
From the [listing](https://www.hardkernel.com/shop/odroid-h3-case-type-5/) you will need these parts:

* G. 1 x 92x92x25mm DC 5V Cooling Fan
* H. 1 x 92mm Stainless Cooling Fan Grill
* I. 2 x 10mm (M/F) PCB Supports
* J. 1 x 40mm (M/F) PCB Supports
* K. 2 x 40mm (F/F) PCB Supports
* L. 4 x M5 PC Fan Screws
* N. 4 x 6mm M3 Truss Head Bolts

Assembling these parts individually may save some money, but purchasing the case directly from Odroid is more convenient for acquiring all necessary components in one go.

### Performance

With [fio 3.33](https://github.com/axboe/fio) (included in TrueNAS Scale):

    root@odroidh3[~]# fio --randrepeat=1 --ioengine=libaio --direct=1 --gtod_reduce=1 --name=test --filename=test.fio --bs=4k --iodepth=64 --size=1G --readwrite=randrw --rwmixread=80
    test: (g=0): rw=randrw, bs=(R) 4096B-4096B, (W) 4096B-4096B, (T) 4096B-4096B, ioengine=libaio, iodepth=64
    fio-3.33
    Starting 1 process
    test: Laying out IO file (1 file / 1024MiB)
    Jobs: 1 (f=1): [m(1)][100.0%][r=50.9MiB/s,w=12.8MiB/s][r=13.0k,w=3289 IOPS][eta 00m:00s]
    test: (groupid=0, jobs=1): err= 0: pid=28483: Thu Jan  2 14:08:15 2025
      read: IOPS=12.3k, BW=48.1MiB/s (50.4MB/s)(819MiB/17040msec)
      bw (  KiB/s): min=12352, max=230160, per=99.98%, avg=49197.18, stdev=47733.65, samples=34
      iops        : min= 3088, max=57540, avg=12299.29, stdev=11933.41, samples=34
      write: IOPS=3081, BW=12.0MiB/s (12.6MB/s)(205MiB/17040msec); 0 zone resets
      bw (  KiB/s): min= 3152, max=56968, per=99.96%, avg=12322.35, stdev=11886.70, samples=34
      iops        : min=  788, max=14242, avg=3080.59, stdev=2971.68, samples=34
      cpu          : usr=2.70%, sys=27.91%, ctx=39665, majf=4, minf=8
      IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=0.1%, 32=0.1%, >=64=100.0%
        submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
        complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.1%, >=64=0.0%
        issued rwts: total=209630,52514,0,0 short=0,0,0,0 dropped=0,0,0,0
        latency   : target=0, window=0, percentile=100.00%, depth=64

    Run status group 0 (all jobs):
      READ: bw=48.1MiB/s (50.4MB/s), 48.1MiB/s-48.1MiB/s (50.4MB/s-50.4MB/s), io=819MiB (859MB), run=17040-17040msec
      WRITE: bw=12.0MiB/s (12.6MB/s), 12.0MiB/s-12.0MiB/s (12.6MB/s-12.6MB/s), io=205MiB (215MB), run=17040-17040msec

And same test with 10M file instead of 1G:

    root@odroidh3[~]# fio --randrepeat=1 --ioengine=libaio --direct=1 --gtod_reduce=1 --name=test --filename=test.fio --bs=4k --iodepth=64 --size=10M --readwrite=randrw --rwmixread=80
    test: (g=0): rw=randrw, bs=(R) 4096B-4096B, (W) 4096B-4096B, (T) 4096B-4096B, ioengine=libaio, iodepth=64
    fio-3.33
    Starting 1 process

    test: (groupid=0, jobs=1): err= 0: pid=28639: Thu Jan  2 14:08:49 2025
      read: IOPS=65.8k, BW=257MiB/s (269MB/s)(8156KiB/31msec)
      write: IOPS=16.8k, BW=65.6MiB/s (68.8MB/s)(2084KiB/31msec); 0 zone resets
      cpu          : usr=6.67%, sys=83.33%, ctx=0, majf=0, minf=8
      IO depths    : 1=0.1%, 2=0.1%, 4=0.2%, 8=0.3%, 16=0.6%, 32=1.2%, >=64=97.5%
        submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
        complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.1%, >=64=0.0%
        issued rwts: total=2039,521,0,0 short=0,0,0,0 dropped=0,0,0,0
        latency   : target=0, window=0, percentile=100.00%, depth=64

    Run status group 0 (all jobs):
      READ: bw=257MiB/s (269MB/s), 257MiB/s-257MiB/s (269MB/s-269MB/s), io=8156KiB (8352kB), run=31-31msec
      WRITE: bw=65.6MiB/s (68.8MB/s), 65.6MiB/s-65.6MiB/s (68.8MB/s-68.8MB/s), io=2084KiB (2134kB), run=31-31msec

And simple dd, onto a ZFS RAID:

    root@odroidh3[/mnt/Primary]# dd if=/dev/zero of=/mnt/Primary/test_file bs=1M count=10K
    10240+0 records in
    10240+0 records out
    10737418240 bytes (11 GB, 10 GiB) copied, 4.15058 s, 2.6 GB/s
    root@odroidh3[/mnt/Primary]# dd if=/mnt/Primary/test_file of=/dev/null bs=1M count=10K
    10240+0 records in
    10240+0 records out
    10737418240 bytes (11 GB, 10 GiB) copied, 1.86648 s, 5.8 GB/s

Feel free to ping me if you want me to run any other tests.

### Power consumption in Watts

Didn't measure it yet. It peaks above 75W during startup, or atleast it crashes during startup and randomly if you provide 75W only. That's why i switched to a proper external 300W PSU.

### Does it support ECC?

No. It can't.

### What software does it run?

It runs TrueNAS Scale.
![truenas](truenas.png)

Side note. Make sure to use the latest intel microcode or you'll end up with VM's freezing here and there pretty often.
You have to have microcode version "24" to have stable virtualization.

    root@nas[~]# grep -m1 microcode /proc/cpuinfo 
    microcode       : 0x24000024

If it shows 0x24000023 then you definitely want to update it.

### Can it be any cheaper?

Yup.

Without disks it's below $500 ;)

Also it should still work pretty decent if you switch:
  * H3+ -> H3 - 36$ less
  * eMMC 64GB -> 32GB - $10 less
  * RAM 32GB -> 16GB - $54 less

So without disks it can be built for about $375.

### Why 5 disks? Can it support more?

Yes, see the original repo. I'm using 5 because it's expensive.

### How to get a lovely case?

All needed parts are listed [there](https://github.com/fenio/ugly-nas/case).
