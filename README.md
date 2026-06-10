# skilldrift

<!-- toc -->
- [Repo structure](#repo-structure)
- [Install](#install)
  - [Interactive Install](#interactive-install)
  - [Specific Skill Install](#specific-skill-install)
- [Acknowledgments](#acknowledgments)
<!-- /toc -->

A small collection of agent skills. Most of them started as someone else's
work. I've changed them to fit the way I use them. Nothing here is original
or polished. If something's useful, take it.

Note that I'll probably rearrange things as I go.

## Repo structure

```
docs/        documentation
skills/      the skills themselves
```

## Install

### Interactive Install

```sh
npx skills add https://github.com/vercel-labs/skills
```

or

```sh
pnx skills add https://github.com/vercel-labs/skills
```

### Specific Skill Install

```sh
npx skills add https://github.com/vercel-labs/skills --skill NAME
```

or

```sh
pnx skills add https://github.com/vercel-labs/skills --skill NAME
```

## Acknowledgments

Thanks to the people who wrote the original skills.

| Repo                        | Skill                         |
|-                            |-                              |
| [obra/superpowers][1]       | `writing-plans`               |
| [obra/superpowers][2]       | `subagent-driven-development` |
| [obra/superpowers][3]       | `executing-plans`             |
| [JuliusBrussee/caveman][4]  | [`caveman-commit`][5]         |

[1]: https://github.com/obra/superpowers/tree/f2cbfbe/skills/writing-plans
[2]: https://github.com/obra/superpowers/tree/f2cbfbe/skills/subagent-driven-development
[3]: https://github.com/obra/superpowers/tree/f2cbfbe/skills/executing-plans
[4]: https://github.com/JuliusBrussee/caveman
[5]: https://github.com/JuliusBrussee/caveman/tree/ed706fa09843f67173be5ff29fb1f3b06f10e904/skills/caveman-commit
