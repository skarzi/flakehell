# Config

FlakeHell can be configured in [pyproject.toml](https://www.python.org/dev/peps/pep-0518/). You can specify any Flake8 options and FlakeHell-specific parameters. Also,

Config resolving order (every next step overwrites previous one):

1. Flake8 legacy configs: `setup.cfg`, `tox.ini`, `.flake8`. Everything you've specified for Flake8 will work for FlakeHell. However, it's strongly recommend to migrate on FlakeHell's own config format, because in Flake8's config you can't specify `plugins`.
1. Modern and beautiful FlakeHell's config in [pyproject.toml](https://www.python.org/dev/peps/pep-0518/). Here you can configure everything for FlakeHell. Use it.
1. CLI flags.

## Plugins

In `pyproject.toml` you can specify `[tool.flakehell.plugins]` table. It's a list of flake8 [plugins](plugins) and associated to them rules.

Key can be exact plugin name or wildcard template. For example `"flake8-commas"` or `"flake8-*"`. FlakeHell will choose the longest match for every plugin if possible. In the previous example, `flake8-commas` will match to the first pattern, `flake8-bandit` and `flake8-bugbear` to the second, and `pycodestyle` will not match to any pattern.

Value is a list of templates for error codes for this plugin. First symbol in every template must be `+` (include) or `-` (exclude). The latest matched pattern wins. For example, `["+*", "-F*", "-E30?", "-E401"]` means "Include everything except all checks that starts with `F`, check from `E301` to `E310`, and `E401`".

## Base

Option `base` allows to specify base config from which you want to inherit this one. It can be path to local config or remote URL. You can specify one path or list of paths as well. For example:

```toml
base = ["https://raw.githubusercontent.com/life4/flakehell/master/pyproject.toml", ".flakehell.toml"]
max_line_length = 90
```

In this example, FlakeHell will read remote config, local config (`.flakehell.toml`), and then current config. So, even if `max_line_length` is specified in some of base configs, it will be overwritten by `max_line_length = 90` from current config.

## Defaults

Most of default parameters are the same as in Flake8. However, some of them are different to make FlakeHell cool:

```toml
# make output beautiful
format='colored'
# 80 chars aren't enough in 21 century
max_line_length=90
```

## Example

```toml
[tool.flakehell]
# optionally inherit from remote config (or local if you want)
base = "https://raw.githubusercontent.com/life4/flakehell/master/pyproject.toml"
# specify any flake8 options. For example, exclude "example.py":
exclude = ["example.py"]
# make output nice
format = "grouped"
# don't limit yourself
max_line_length = 120
# show line of source code in output
show_source = true

# list of plugins and rules for them
[tool.flakehell.plugins]
# include everything in pyflakes except F401
pyflakes = ["+*", "-F401"]
# enable only codes from S100 to S199
flake8-bandit = ["-*", "+S1??"]
# enable everything that starts from `flake8-`
"flake8-*" = ["+*"]
# explicitly disable plugin
flake8-docstrings = ["-*"]
```

See [Flake8 documentation](http://flake8.pycqa.org/en/latest/user/configuration.html) to read more about Flake8-specific configuration.
