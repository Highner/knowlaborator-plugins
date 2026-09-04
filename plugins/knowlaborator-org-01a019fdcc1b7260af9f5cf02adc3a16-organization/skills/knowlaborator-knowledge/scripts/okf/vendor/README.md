# Vendored PyYAML

This directory contains the pure-Python package from PyYAML 6.0.3.

- Source: `https://files.pythonhosted.org/packages/05/8e/961c0007c59b8dd7729d542c61a4d537767a59645b82a0b521206e1e25c2/pyyaml-6.0.3.tar.gz`
- Source SHA-256: `d76623373421df22fb4cf8817020cbb7ef15c725b9d5e45f17e189bfc384190f`
- Included source subtree: `lib/yaml/`
- License: MIT; see `PyYAML-LICENSE`

The OKF validator prepends this directory to `sys.path` and verifies
`yaml.__version__` before parsing. Do not replace it with a host-installed
package or add runtime installation steps to the skill.
