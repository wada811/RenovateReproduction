# RenovateReproduction

Renovate doesn't support [Repository content filtering](https://docs.gradle.org/current/userguide/declaring_repositories.html#sec:repository-content-filtering) of predefined registry in Gradle.

# Current behavior

Renovate can't parse predefined registry have repository content filtering (Code.1).

https://github.com/wada811/RenovateReproduction/blob/1064d9b8e82714efae8c1c03935dbd8e1fd70660/settings.gradle#L17-L23
Code.1: predefined registry have repository content filtering

Therefore, PR is not created or is auto closed (Image.1) after changing registry to have repository content filtering (Code.2).

<table style="border: 0;">
  <tr>
    <td>
<a href="https://github.com/wada811/RenovateReproduction/pulls?q=is%3Apr+is%3Aclosed"><img width="640" alt="image" src="https://user-images.githubusercontent.com/1378923/157270734-925a6df1-477d-44d6-8c77-5c2af4ad1df8.png"><br>
Image.1: Closed PRs</a>
    </td>
    <td>
<a href="https://github.com/wada811/RenovateReproduction/commit/1064d9b8e82714efae8c1c03935dbd8e1fd70660#diff-7f825392aa37acd1cee0c2e7b9bb7366ad6eac64f3e6cdd816e156bcb69d30deL11-R23"><img width="430" alt="image" src="https://user-images.githubusercontent.com/1378923/157269795-27a5d539-8d06-4fdb-a938-30e0a2b00ee9.png"><br>
Code.2: Change registry to have repository content filtering</a>
    </td>
  </tr>
</table>

# Expected behavior

Renovate can parse predefined registry that have repository content filtering.
And, PR is created or is not closed.

# Reproduction

1. Fork this repository.
2. Self hosting with docker below this configuration.
    ```
    module.exports = {
      token: 'your token',
      repositories: ['you/RenovateReproduction'],
    };
    ```
3. run self hosting (v32.0.3).
4. PR is not created.

# Debug log

## tokens

```
DEBUG: tryMatch: packageFile: settings.gradle (repository=wada811/RenovateReproduction)
       "tokens": [
         {"type": "word", "offset": 448, "value": "google"},
         {"type": "leftBrace", "offset": 455, "value": "{"},
         {"type": "word", "offset": 469, "value": "content"},
         {"type": "leftBrace", "offset": 477, "value": "{"},
         {"type": "word", "offset": 495, "value": "includeGroupByRegex"},
         {"type": "string", "value": "androidx\\..*", "offset": 516},
         {"type": "word", "offset": 547, "value": "includeGroupByRegex"},
         {"type": "string", "value": "com\\.android.*", "offset": 568},
         {"type": "word", "offset": 601, "value": "includeGroupByRegex"},
         {"type": "string", "value": "com\\.google\\..*", "offset": 622},
         {"type": "rightBrace", "offset": 653, "value": "}"},
         {"type": "rightBrace", "offset": 663, "value": "}"},
         {"type": "word", "offset": 673, "value": "maven"},
         {"type": "leftBrace", "offset": 679, "value": "{"},
         {"type": "word", "offset": 693, "value": "url"},
         {"type": "string", "value": "https://jitpack.io", "offset": 698},
         {"type": "word", "offset": 730, "value": "content"},
         {"type": "leftBrace", "offset": 738, "value": "{"},
         {"type": "word", "offset": 756, "value": "includeGroupByRegex"},
         {"type": "string", "value": "com\\.github\\..*", "offset": 777},
         {"type": "rightBrace", "offset": 808, "value": "}"},
         {"type": "rightBrace", "offset": 818, "value": "}"},
         {"type": "word", "offset": 828, "value": "mavenCentral"},
         {"type": "leftParen", "offset": 840, "value": "("},
         {"type": "rightParen", "offset": 841, "value": ")"},
         {"type": "rightBrace", "offset": 847, "value": "}"},
         {"type": "rightBrace", "offset": 849, "value": "}"},
         {"type": "word", "offset": 851, "value": "rootProject"},
         {"type": "dot", "offset": 862, "value": "."},
         {"type": "word", "offset": 863, "value": "name"},
         {"type": "assignment", "offset": 868, "value": "="},
         {
           "type": "string",
           "value": "RenovateRepositoryContentFiltering",
           "offset": 871
         },
         {"type": "word", "offset": 907, "value": "include"},
         {"type": "string", "offset": 916, "value": ":app"}
       ]
```

## matcher, tokenMap

```
DEBUG: tryMatch: packageFile: settings.gradle (repository=wada811/RenovateReproduction)
       "matchers": [
         {
           "matchType": "word",
           "matchValue": ["mavenCentral", "jcenter", "google", "gradlePluginPortal"],
           "tokenMapKey": "registryName"
         },
         {"matchType": "leftParen"},
         {"matchType": "rightParen"},
         {
           "matchType": ["semicolon", "rightBrace", "word", "string", "interpolation"],
           "lookahead": true
         }
       ],
       "tokenMap": null
```

## registryUrl

```
DEBUG: extractAllPackageFiles: registryUrl: https://plugins.gradle.org/m2/ (repository=wada811/RenovateReproduction)
DEBUG: extractAllPackageFiles: registryUrl: https://repo.maven.apache.org/maven2 (repository=wada811/RenovateReproduction)
DEBUG: extractAllPackageFiles: registryUrl: https://jitpack.io (repository=wada811/RenovateReproduction)
DEBUG: extractAllPackageFiles: registryUrl: https://repo.maven.apache.org/maven2 (repository=wada811/RenovateReproduction)
```

## Full

[debug.log](https://raw.githubusercontent.com/wada811/RenovateReproduction/main/debug.log)


