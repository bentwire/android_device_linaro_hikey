CROSS_COMPILE?=arm-linux-gnueabihf-
CC=$(CROSS_COMPILE)gcc
CFLAGS=-march=armv7-a
LD=$(CROSS_COMPILE)ld
OBJCOPY=$(CROSS_COMPILE)objcopy
BL1=bl1.bin
PTABLE_LST:=aosp-4g aosp-8g linux-4g linux-8g swap-8g

all: l-loader.bin ptable.img

%.o: %.S $(DEPS)
	$(CC) $(CFLAGS) -c -o $@ $<

l-loader.bin: start.o $(BL1)
	$(LD) -Bstatic -Tl-loader.lds -Ttext 0xf9800800 start.o -o loader
	$(OBJCOPY) -O binary loader temp
	python gen_loader_hikey.py -o $@ --img_loader=temp --img_bl1=$(BL1) --img_ns_bl1u=recovery-fastboot.bin
	rm -f loader temp

ptable.img:
	for ptable in $(PTABLE_LST); do \
		PTABLE=$${ptable} SECTOR_SIZE=512 bash -x generate_ptable.sh;\
		mv prm_ptable.img ptable-$${ptable}.img;\
	done

clean:
	rm -f *.o *.img l-loader.bin
