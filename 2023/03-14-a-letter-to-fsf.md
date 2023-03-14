# About (L)GPL in Practice: We distribute binaries, but where are the sources?

## 前言

LGPL 当中存在一个问题，如果一个软件引用了一个 LGPL 开源库并且分发编译后的二进制的话，那么就要同时分发所引用二进制库的源代码，不得省略，不得由自己不可控的第三方提供，同时必须保持可用。而这一点，我敢说 90% 的项目都做不到。

我给 FSF 写了一封信，就这个问题狠狠拷打他们——大概。更大概率应该只是英语文章写作练习。

## 正文

Hello!

After counsulting the (L)GPL license, I realised a serious problem that does prevent me from using (L)GPL libraries in my work. These are GPLv3 section 6 and LGPLv3 section 4.(d).

I come to this as I'm searching a library that can be used into my C++ learning. And as most people will except, Qt becomes my first choice. I read though the open source (sorry for the misuse) FAQ provided by the Qt website, which claims that if a software binary dynamically links Qt's LGPL licensed components, the source code of the exact version of the library must be also distributed in an original form. And they state that the source code must be hosted by the developer who uses the library, and a link to Qt's website is not sufficient. I feel strange, because it just differs from my experience. I use free softwares written in Qt, but they never distribute Qt's source code.

I continue to find more about this and LGPL section 4.(d) and therefore GPLv3 section 6. As stated in GPLv3 section 6, any developer who delivers binary form of their software should provide the corresponding source code. That's OK, and that's the heart of GPL. But when combined with LGPL, the problem arises. The LGPL license requires developers to distribute the source code of LGPL-licensed libraries under GPLv3 section 6, and GPLv3 section 6 requires the developer to ensure the availability of it. It sounds perfect, but is it?

OK, so let's jump out from GNU's perspective (sorry), and view the problem as a normal open source (sorry again) developer, who may and may not have a job. Modern software development no longer relies on single or a few libraries. It relies on a lot. I'm developing a good piece of software, and I choose a successful library, let's say Qt. And a lot of other thing, licensed under (L)GPL and other open source licenses. Good! I published my source code so it complies with GPL's rule (at least this is what I think of GPL), and published my binary. Perfect isn't it!

But something is wrong. I published the source code of my software, but not the source code of dependencies of my software. And dependencies of dependencies. And dependencies of dependencies of dependencies... Stop that's enough. That violates (L)GPL. If I use GTK, you FSF can accuse me. And if I use Qt, the Qt company can accuse me. Note, here "I" refers to a personal developer who doesn't notice the restriction. So I won't use (L)GPL licensed libraries in my next software. Just in case of someone's accusing.

But what about those who are big enough that can't ignore this restriction? The same applies. I use MLT's shotcut for video editing. Big and famous enough? They don't provide a lot of dependencies' source code. They do include some though, but they says some of the dependencies should be fetched by yourself. That's something not provided! And that is just one of the countless examples. Someone says "we can get them ourselves", but what about GPL section 6.(d)? "Regardless of what server hosts the Corresponding Source, you remain obligated to ensure that it is available for as long as needed to satisfy these requirements." How can they ensure it?

The only way to achieve this is through a centeralised software distributing platform. Yes, I'm talking about distributions. They can ensure the source can be fetched as long as the binary can also be fetched. But why! The modern software development has proved that the "package manager" model has its restrictions. Libraries conflict with each other. Upgrading a software means upgrading the whole system. Using the old software while the newest version haven't even make their way to the repository. Why we must follow this model? And what about third party repositories? Is the main repository controllable by them?

And a thing that must be considered is, although you complains every day, people use Windows and macOS. There isn't a package manager which provide source as well! Every developer will have to manage to provide source by themselves. No one wants to do that. REALLY.

You may talk about your vision and your wish to create a completely hackable free world, but GPL is something that is already in use. And it doesn't work well, indeed. Maybe I'm being a little too harsh, but I can say that the current version of (L)GPL, released in 2007, is already too old. Too old to license today's products. Too old to push the free software society forward. And too old that won't be a choice when an ordinary software developer releases their work. I know you hate "modern", but that will eventually be a stumbling block of free software campaign. And that's a shame.

GNU is something ideal. I love it, and I hope GNU can eventually reach its goal. But I should also consider the price for choosing idealism. Some words may need changes, and exceptions may be made. GPL is great, but it is just not perfect.

And, sorry for my spliting hairs. I write too much on this topic.

Best wishes,

ZeroAurora
