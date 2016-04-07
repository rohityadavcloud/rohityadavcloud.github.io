

sudo pip install --upgrade paramiko mysql-connector-python nose ddt requests

mvn -P developer -pl :cloud-marvin -Dnoredist

sudo pip install --upgrade tools/marvin/dist/Marvin-*-SNAPSHOT.tar.gz

$ nosetests -p
Plugin capture
Plugin failuredetail
Plugin xunit
Plugin deprecated
Plugin skip
Plugin multiprocess
Plugin logcapture
Plugin coverage
Plugin marvin
Plugin attributeselector
Plugin doctest
Plugin profile
Plugin id
Plugin allmodules
Plugin collect-only
Plugin isolation
Plugin pdb

Sync:
sudo mvn -Pdeveloper,marvin.sync -Dendpoint=localhost -pl :cloud-marvin
