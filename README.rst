nupic.core (https://github.com/numenta/nupic.core) wouldn't build in the stock
manylinux1_x86_64 docker image because the centos5-based docker image lacks some
system headers and symbols required by the capnproto library, a subcomponent of
nupic.core (see https://github.com/sandstorm-io/capnproto/issues/350,
https://github.com/pypa/manylinux/issues/75, and
https://mail.python.org/pipermail/wheel-builders/2016-July/000175.html).

This docker image build is produced from numenta's fork of
https://github.com/pypa/manylinux. The fork bases the docker image on centos6,
which has the necessary headers that enable build of capnproto (and rest of
nupic.core) to succeed.

This docker image is built by executing

`[sudo] docker build --rm -t quay.io/numenta/manylinux1_x86_64_centos6 -f docker/Dockerfile-x86_64 docker/`

from the root directory of https://github.com/numenta/manylinux

I pushed an initial manually-built docker image to quay.io/numenta/manylinux1_x86_64_centos6,
which enables the build of nupic.core manylinux wheel.

NOTE: For production, a Bamboo CI pipeline needs to be created to track changes
to master and rebuild the docker image.


**IMPORTANT** Since this version of manylinux1_x86_64 is based on centos-6
(instead of centos-5), it is technically non-compliant with manylinux1 policy of
https://www.python.org/dev/peps/pep-0513/, which requires centos-5 system
libraries. However, as suggested in discussions
https://mail.python.org/pipermail/wheel-builders/2016-July/000175.html, the
wheels produced with the centos-6-based image should still be compatible with
many distros (except centos-5), and a future manylinux2 policy may indeed be
based on centos-6 as well.

See also [proof-of-concept nupic.bindings manylinux wheel build PR](https://github.com/numenta/nupic.core/pull/1001).
