# GPAW install
濱本先生の手順通り実行する。

この際、各々のバージョンを記載通り古い状態で扱う。

http://www-cp.prec.eng.osaka-u.ac.jp/hamamoto/gpaw_21.1.html

<br />

---
## Install Libxc
バージョンはlibxc-4.3.4

prefix以降を/home/(your-group)/(your-id)/lib/libxc-4.3.4としています。

```
$ mkdir src lib
$ cd src
$ wget http://www.tddft.org/programs/libxc/down.php?file=4.3.4/libxc-4.3.4.tar.gz
$ tar xzvf libxc-4.3.4.tar.gz
$ cd libxc-4.3.4
$ ./configure CC=icc CFLAG="-O2 -fPIC" --enable-shared --disable-fortran --prefix=/home/<your-group>/<your-id>/lib/libxc-4.3.4
$ make
$ make install
```
---
## Create a virtual environment and install required packages
モジュールのバージョンを下げました。

```
$ module list
 1) intel_compiler/2020.2.254   2) intel_mpi/2020.2.254   3) intel_mkl/2020.2.254
 ```

 仮想環境を設定します。

 各々のパッケージのバージョンを指定したテキストファイルを作成し、読み込みます。

 ```
 Package         Version
--------------- -------
ase             3.21.1
attrs           20.3.0
cycler          0.10.0
cymem           2.0.5
Cython          0.29.22
decorator       4.4.2
iniconfig       1.1.1
kiwisolver      1.3.1
matplotlib      3.3.4
mpi4py          3.0.3
numpy           1.20.1
packaging       20.9
Pillow          8.1.2
pip             21.0.1
pluggy          0.13.1
py              1.10.0
pyparsing       2.4.7
pytest          6.2.2
python-dateutil 2.8.1
scipy           1.6.1
setuptools      41.6.0
six             1.15.0
toml            0.10.2
```

ここで濱本先生のドキュメントと比較してaseとpipを削除し、
numpy=1.19.5としています。

---

## Install GPAW
バージョンを21.1.0に下げています。

```
(py38env) $ mkdir -p ~/GPAW/src/
(py38env) $ cd ~/GPAW/src/
(py38env) $ wget https://pypi.org/packages/source/g/gpaw/gpaw-21.1.0.tar.gz
(py38env) $ tar xzvf gpaw-21.1.0.tar.gz
(py38env) $ cd gpaw-21.1.0
(py38env) $ vi siteconfig.py
```

siteconfig.pyの中身は濱本先生のものと同じです。

```
(py38env) $ python setup.py build
(py38env) $ python setup.py install
```

---

## Use pytest to test GPAW
濱本先生と同じpytest.shを作成します。

src/でジョブを実行します。

```
(py38env) $ cd src/
(py38env) $ sbatch pytest.sh
```

成功しました。
あとは新しいバージョンで確かめていきます。