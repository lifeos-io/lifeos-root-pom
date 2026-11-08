# lifeos-root-pom

Parent POM for every Life OS repository. It contains only:

- Versions: Java 25, Spring Boot 4.1.2, Spring Cloud 2025.1.4, Spring AI 2.0.0
- `dependencyManagement`: the Spring Cloud and Spring AI BOMs
- Shared build rules (enforcer: Java 25+, Maven 3.9+)
- `distributionManagement` for GitHub Packages

It declares no dependencies; each service picks its own in its own POM.

## How it is managed

1. **It has its own version.** The root POM is an artifact: `com.lifeos:lifeos-root-pom:<version>`.
   Services reference it as a parent with a fixed version (`<relativePath/>`) and never look at a sibling folder.
2. **Release = tag.** Pushing a tag such as `v1.2.0` triggers `.github/workflows/publish.yml`, which takes the
   version from the tag and runs `mvn deploy` to GitHub Packages. `lifeos-base` and `lifeos-contracts` use the same flow.
3. **Services upgrade on their own.** When a new root POM is published (e.g. a Spring Boot patch), each service
   bumps its parent version in its own PR (`mvn versions:update-parent`). Renovate can open these PRs automatically.
4. **`lifeos-base` and `lifeos-contracts` versions are not managed here.** Each service keeps
   `lifeos-base.version` and `lifeos-contracts.version` in its own POM, so a new base release does not force a new root POM release.
5. **During development** work with `0.1.0-SNAPSHOT` and `mvn install` locally; switch to fixed versions (`1.0.0`) once CI builds the services.

Before the first release, replace the `github.owner` property in `pom.xml` with your GitHub username.
