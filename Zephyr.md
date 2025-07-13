# Zephyr RTOS Build Procedure 
### https://docs.zephyrproject.org/latest/develop/getting_started/index.html
### https://www.youtube.com/watch?v=nrdpRzF5tlU
## sudo apt update && sudo apt upgrade
## python3 -m venv ./zephyrproject/.venv
## source ./zephyrproject/.venv/bin/activate
## sudo apt install --no-install-recommends git cmake ninja-build gperf   ccache dfu-util device-tree-compiler wget python3-dev python3-venv python3-tk   xz-utils file make gcc gcc-multilib g++-multilib libsdl2-dev libmagic1
## pip install west
## west init ./zephyrproject/
## cd zephyrproject/
## west update
## west zephyr-export
## west packages pip --install
## cd zephyr
## west sdk install
## west build -p always -b nrf52833dk_nrf52833 samples/basic/blinky  (will not work in 4.0.1 -rc3)
## west build -p always -b nrf52833dk/nrf52833 samples/basic/blinky
## cd build/zephyr/   (image , hex file)
## cd ../../samples/basic/blinky  (Blinky file)
