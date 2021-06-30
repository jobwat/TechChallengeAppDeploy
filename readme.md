# TechChallengeAppDeploy

This is a deployment proposition to the [TechChallengeApp](https://github.com/servian/TechChallengeApp)

## Documentation structure

- [readme.md](readme.md)
  Current file

- [doc/adr](doc/adr) folder
  Architecture Design Records (ADR), details on why can be found in the [first entry](adr/0001-record-architecture-decisions.md)

  Naming convention: `####-<decision title>` where the first 4 digits are iterated by 1 for each record.


## Chatter

### Happy discoveries during this challenge

- ADR (Architecture Design Records) concept and implementation
- Github release helper [ghr](github.com/tcnksm/ghr) to push release with artifacts

### Points I'd make clearer

- [.circleci/config.yml#L147](https://github.com/servian/TechChallengeApp/blob/cd0c072cb11f534dfe1b673b5ec439b91e2d4da9/.circleci/config.yml#L147)
  If current version has already been published, it's currently silenced. I'd output a little message to clarify debugging.
