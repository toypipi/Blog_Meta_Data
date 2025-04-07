---
title: Cookies、Session Storage、和 Local Storage
categories:
  - null
tags:
  - null
date: 2023-08-24 16:51:22
---

---

Cookies、Session Storage、和 Local Storage 是用于在浏览器中存储数据的不同机制，它们有各自的用途和特点。

**Cookies**:

1. **存储容量**: Cookies 最大可存储大约 4KB 的数据。
2. **生命周期**: Cookies 可以设置过期时间，可以是会话级别的（浏览器关闭后失效）或长期的（根据设置的过期时间）。
3. **跨域请求**: Cookies 可以在同一域名下的不同路径和子域之间共享。
4. **安全性**: Cookies 可以设置为 HTTP Only 和 Secure，以提高安全性，但仍然容易受到跨站点脚本攻击（XSS）的威胁。
5. **用途**: 通常用于存储少量的身份验证信息、会话标识、用户首选项等。

<!-- more -->

**Session Storage**:

1. **存储容量**: Session Storage 可以存储大约 5-10MB 的数据，比 Cookies 更大。
2. **生命周期**: 存储在 Session Storage 中的数据在浏览器会话结束后（即浏览器关闭）会被删除，它是会话级别的存储。
3. **跨域请求**: Session Storage 只在同一标签页或窗口中共享，不会跨标签页或窗口。
4. **安全性**: Session Storage 不易受到跨站点脚本攻击（XSS）的威胁，因为它的作用范围有限于单个窗口或标签页。
5. **用途**: 适用于在单个浏览器会话期间需要存储临时数据的情况，例如表单数据。

**Local Storage**:

1. **存储容量**: Local Storage 可以存储大约 5-10MB 的数据，与 Session Storage 相似。
2. **生命周期**: 存储在 Local Storage 中的数据不会在浏览器关闭后失效，它是长期的存储。
3. **跨域请求**: Local Storage 也在同一域名下的不同路径和子域之间共享。
4. **安全性**: 与 Cookies 类似，Local Storage 可以受到跨站点脚本攻击（XSS）的威胁。
5. **用途**: 适用于需要长期存储的数据，例如用户首选项、本地缓存等。

总结一下：

- Cookies 适用于小容量的数据，可以用于跨域请求，但受到安全性和存储容量的限制。
- Session Storage 适用于会话级别的数据，不跨标签页或窗口，相对较安全，但会话结束后数据会丢失。
- Local Storage 适用于长期存储的数据，可以跨标签页或窗口，但受到安全性的限制。

选择适当的存储机制取决于你的具体需求，包括数据大小、生命周期、跨域需求和安全性考虑。

