This guide will help you to compile your own marketmaker binary in Linux with static nanomsg.

## Install `nanomsg`
```shell
cd ~
git clone https://github.com/nanomsg/nanomsg
cd nanomsg
cmake . -DNN_TESTS=OFF -DNN_ENABLE_DOC=OFF
make -j2
sudo make install
sudo ldconfig
```

## Clone SuperNET repo and compile
```shell
cd ~
git clone https://github.com/jl777/SuperNET
cd ~/SuperNET/iguana
git checkout dev
./m_LP_StaticNanoMsg
make -f m_mm_StaticNanoMsg -j2 all
```
Once all done, you should be able to find both `iguana` and `marketmaker` static binaries in `~/SuperNET/agents/` dir.