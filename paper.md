---
title: mdspan.at()
document: PnnnnR0
date: 2023-11-29
audience:
- Library Evolution Working Group (LEWG)
- SG23 Safety and Security
author:
  - name: Stephan Lachnit
    email: <stephan.lachnit@desy.de>
toc: true
---

# Introduction

*TODO*

`std::mdspan::at` element access with boundary checking as alternative to element access via `std::mdspan::operator[]`.

# Motivation

## Safety

*TODO*

This new method is safe in the sense that it has defined behavior instead of undefined behavior. Further, the defined behavior is one that can be caught in the code by catching the exception.

## Consistency

*TODO*

Consistency with `std::span::at` [@P2821R4] and other containers.

# Impact On the Standard

*TODO*

Low impact:

- Design similar to [@P2821R4] and [@P0009R18]
- Signature identical to multi-dimensional `operator[]` in [@P0009R18]
- While `.at()` previously not used with multi-dimensional arguments, this is also true for the multi-dimensional `operator[]` before it was introduced in [@P2128R6]

# Wording

The wording is relative to [@N4964].

In 17.3.2 ([@version.syn]), add:

::: add

> ```
> #define __cpp_lib_mdspan_at YYYYMML @_// also in_@ <mdspan>
> ```

:::

Adjust the placeholder value as needed so as to denote this proposal's date of adoption.

In 24.7.3.6.1 ([@mdspan.mdspan.overview]), add the following immediately after the subscript operators:

::: add

> ```
> template<class... OtherIndexTypes>
>   constexpr reference at(OtherIndexTypes... indices) const;
> template<class OtherIndexType>
>   constexpr reference at(span<OtherIndexType, rank()> indices) const;
> template<class OtherIndexType>
>   constexpr reference at(const array<OtherIndexType, rank()>& indices) const;
> ```

:::

In 24.7.3.6.3 ([@mdspan.mdspan.members]), add the following immediately after the subscript operators:

::: add

> ```
> template<class... OtherIndexTypes>
>   constexpr reference at(OtherIndexTypes... indices) const;
> ```
> [7]{.pnum} *Effects:* Equivalent to: `operator[](OtherIndexTypes... indices)` with bounds checking
>
> [8]{.pnum} *Throws:* `out_of_range` if any index exceeds the extent for that dimension.
>
> ```
> template<class OtherIndexType>
>   constexpr reference at(span<OtherIndexType, rank()> indices) const;
> ```
> ```
> template<class OtherIndexType>
>   constexpr reference at(const array<OtherIndexType, rank()>& indices) const;
> ```
> [9]{.pnum} *Effects:* Let `P` be a parameter pack such that\
> `is_same_v<make_index_sequence<rank()>, index_sequence<P...>>`\
> is `true`. Equivalent to:\
> `return at(as_const(indices[P])...);`
>
> [10]{.pnum} *Throws:* `out_of_range` if any index exceeds the extent for that dimension.

:::

# Reference Implementation

*TODO*

See [@Implementation]

---
references:
  - id: mdspan.mdspan.overview
    citation-label: mdspan.mdspan.overview
    URL: https://eel.is/c++draft/mdspan.mdspan.overview
  - id: mdspan.mdspan.members
    citation-label: mdspan.mdspan.members
    URL: https://eel.is/c++draft/mdspan.mdspan.members
  - id: version.syn
    citation-label: version.syn
    URL: https://eel.is/c++draft/version.syn
  - id: Implementation
    citation-label: Implementation
    URL: https://github.com/kokkos/mdspan/pull/302
---
