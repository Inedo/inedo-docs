---
title: Bower (JavaScript)
subtitle: Bower Feeds in ProGet
sequence: 400
keywords: proget,feeds, bower

---

:::attention {.technical}
Bower feeds are deprecated as of ProGet 5.2. You can still create and use a Bower feed,
but as [Bower itself is no longer recommended for new projects](https://bower.io/blog/2017/how-to-migrate-away-from-bower/),
we may drop support for Bower entirely in a future release.
:::


A Bower feed is an index of Bower packages and repositories. Since Bower packages are themselves generally contained within Git repositories, a Bower feed package is simply a reference to a source code repository.
