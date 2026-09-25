# Changelog

## [2.0.0](https://github.com/eriksaulnier/loupe-workflows/compare/v1.4.0...v2.0.0) (2026-09-25)


### ⚠ BREAKING CHANGES

* **review:** the loupe_version input is removed. A caller that still passes it fails with "Invalid input". Drop it from the caller's

### Features

* **review:** add a sticky input that edits one review per PR ([#25](https://github.com/eriksaulnier/loupe-workflows/issues/25)) ([2f56e70](https://github.com/eriksaulnier/loupe-workflows/commit/2f56e7065d7d74c132f7b370ea196536362ae858)), closes [#22](https://github.com/eriksaulnier/loupe-workflows/issues/22)
* **review:** install loupe with its own pinned action ([#20](https://github.com/eriksaulnier/loupe-workflows/issues/20)) ([b8c115d](https://github.com/eriksaulnier/loupe-workflows/commit/b8c115d1352475411c30322525d53f3912da6fba))
* **review:** re-pin loupe to v0.11.0 ([#24](https://github.com/eriksaulnier/loupe-workflows/issues/24)) ([ccc2bdd](https://github.com/eriksaulnier/loupe-workflows/commit/ccc2bdd009175482ffd5e45dd7b964288b4a1dcd))
* **review:** review each push to an open pull request ([#18](https://github.com/eriksaulnier/loupe-workflows/issues/18)) ([f9f38bd](https://github.com/eriksaulnier/loupe-workflows/commit/f9f38bdeafb42853a50322b0278ec43a448999f7))

## [1.4.0](https://github.com/eriksaulnier/loupe-workflows/compare/v1.3.0...v1.4.0) (2026-09-24)


### Features

* **review:** list the head's files and count every file read ([#16](https://github.com/eriksaulnier/loupe-workflows/issues/16)) ([edad758](https://github.com/eriksaulnier/loupe-workflows/commit/edad75856354f5ec4442dfe52f98dc7fbadcc7d2))

## [1.3.0](https://github.com/eriksaulnier/loupe-workflows/compare/v1.2.0...v1.3.0) (2026-09-23)


### Features

* **review:** bump loupe to v0.10.0 ([#14](https://github.com/eriksaulnier/loupe-workflows/issues/14)) ([e600804](https://github.com/eriksaulnier/loupe-workflows/commit/e600804b27d27cdea8852960452416ec05916617))

## [1.2.0](https://github.com/eriksaulnier/loupe-workflows/compare/v1.1.1...v1.2.0) (2026-09-22)


### Features

* **review:** append a coverage line computed from the agent's trace ([#11](https://github.com/eriksaulnier/loupe-workflows/issues/11)) ([8148250](https://github.com/eriksaulnier/loupe-workflows/commit/81482502fecc41fdc0495e22e377256ee455f232))
* **review:** bump loupe to v0.9.0 and use what it reports ([#13](https://github.com/eriksaulnier/loupe-workflows/issues/13)) ([87afca3](https://github.com/eriksaulnier/loupe-workflows/commit/87afca32805b09d40dd98e855885ce84b2144b19))

## [1.1.1](https://github.com/eriksaulnier/loupe-workflows/compare/v1.1.0...v1.1.1) (2026-09-18)


### Documentation

* describe releases and pins as they now work ([5ffee4d](https://github.com/eriksaulnier/loupe-workflows/commit/5ffee4d3cf6dbade3ee1c193861e52a9d243bd2f))

## [1.1.0](https://github.com/eriksaulnier/loupe-workflows/compare/v1.0.4...v1.1.0) (2026-09-18)


### Features

* ask the reviewer for impact and a valid severity ([2597b65](https://github.com/eriksaulnier/loupe-workflows/commit/2597b651318f40687a7a192f106af3646977e236))
* review this repository with its own workflow ([3c420d5](https://github.com/eriksaulnier/loupe-workflows/commit/3c420d573c0a1ec45165c510858d5a98bfb8ec5f))


### Continuous Integration

* **release:** cut releases with release-please ([0ba35f6](https://github.com/eriksaulnier/loupe-workflows/commit/0ba35f63ac2c07e595efe5bcf4ea363a5f48fab6))
