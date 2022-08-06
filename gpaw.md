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
$ python3.8 -m venv py38env
$ source ~/py38env/bin/activate
 (py38env) $ vi requirements.txt
 
Package         Version
--------------- -------
ase==3.21.1
attrs==20.3.0
cycler==0.10.0
cymem==2.0.5
Cython==0.29.22
decorator==4.4.2
iniconfig==1.1.1
kiwisolver==1.3.1
matplotlib==3.3.4
mpi4py==3.0.3
numpy==1.19.5
packaging==20.9
Pillow==8.1.2
pluggy==0.13.1
py==1.10.0
pyparsing==2.4.7
pytest==6.2.2
python-dateutil==2.8.1
scipy==1.6.1
setuptools==41.6.0
six==1.15.0
toml==0.10.2

(py38env) $ pip install -r requirements.txt
```

ここで濱本先生のドキュメントと比較してpipを削除し、
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

src/gpaw-21.1.0/でジョブを実行します。

```
(py38env) $ cd src/gpaw-21.1.0/
(py38env) $ sbatch pytest.sh
(py38env) $ less pytest.log

============================= test session starts ==============================
platform linux -- Python 3.8.12, pytest-6.2.2, py-1.10.0, pluggy-0.13.1 -- /home/k0227/k022707/py38env/bin/python3.8
cachedir: .pytest_cache
rootdir: /home/k0227/k022707/GPAW/src/gpaw-21.1.0
collecting ... collected 423 items

gpaw/test/test_AA_enthalpy.py::test_exx_AA_enthalpy SKIPPED (world.s...) [  0%]
gpaw/test/test_Gauss.py::test_Gauss PASSED                               [  0%]
・
・
・
================================ GPAW-MPI stuff ================================
size: 1
=========================== short test summary info ============================
FAILED gpaw/test/test_Hubbard_U.py::test_Hubbard_U - RuntimeError: Could not ...
FAILED gpaw/test/test_Hubbard_U_Zn.py::test_Hubbard_U_Zn - RuntimeError: Coul...
・
・
・
FAILED gpaw/test/xc/test_xcatom.py::test_xc_xcatom - RuntimeError: Could not ...
ERROR gpaw/test/exx/test_kpts.py::test_kpts[EXX] - RuntimeError: Could not fi...
ERROR gpaw/test/exx/test_kpts.py::test_kpts[PBE0] - RuntimeError: Could not f...
ERROR gpaw/test/exx/test_kpts.py::test_kpts[HSE06] - RuntimeError: Could not ...
ERROR gpaw/test/lrtddft/test_1.py::test_finegrid - RuntimeError: Could not fi...
ERROR gpaw/test/lrtddft/test_1.py::test_velocity_form - RuntimeError: Could n...
ERROR gpaw/test/lrtddft/test_1.py::test_singlet_triplet - RuntimeError: Could...
ERROR gpaw/test/lrtddft/test_1.py::test_spin - RuntimeError: Could not find r...
ERROR gpaw/test/lrtddft/test_1.py::test_io - RuntimeError: Could not find req...
ERROR gpaw/test/lrtddft/test_dielectric.py::test_get - RuntimeError: Could no...
ERROR gpaw/test/lrtddft/test_dielectric.py::test_write - RuntimeError: Could ...
ERROR gpaw/test/utilities/test_wannier_ethylene.py::test_ethylene_energy - Ru...
ERROR gpaw/test/utilities/test_wannier_ethylene.py::test_wannier_centers - Ru...
ERROR gpaw/test/utilities/test_wannier_ethylene.py::test_wannier_centers_gpw
= 265 failed, 109 passed, 35 skipped, 1 xfailed, 108 warnings, 13 errors in 787.71s (0:13:07) =
```

上手くいったと思いきやエラーが出ていたので、以下のように文書を追加し（追加しなくてもいいかもしれない）

```
$ vi gpaw/basis_data.py

      import matplotlib as mpl
        mpl.use('Agg')

$ sbatch pytest.sh -lf
```
i8cpuではなくF1cpuで再度pytestを実行すると

同じ結果になりました。今色々試しています。結果が分かり次第連絡します。

各々新しいバージョンで確かめる必要もあるかと思います。
