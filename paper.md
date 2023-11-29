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

This paper proposes element access with bounds checking to `std::mdspan` via `at()` member functions.

# Motivation

## Safety

The new `at()` member functions provide memory-safe element access to `std::mdspan`, and thus have defined behavior. Out-of-bound access can be caught by catching the `std::out_of_range` exception.

## Consistency

In [@P2821R4], which has been accepted into C++26, element access with bounds checking via `at()` has been added to `std::span`. One of the main motivations for this change was consistency with other containers that have element access with bounds checking via `at()`. Similarily, such element access should be added to `std::mdspan`.

# Impact On the Standard

The impact of this proposal on the standard is low. The proposed function signatures for the `at()` member functions are identical to the function signatures for the subscript operators as proposed in [@P0009R18].

One consideration is that the `.at()` operator has previously not beed used with multi-dimensional arguments, however this was also true before the possibility was introduced in [@P2128R6].

# Wording

The wording is relative to [@N4964].

In 17.3.2 ([[version.syn]](https://eel.is/c++draft/version.syn)), add:

::: add

> ```
> #define __cpp_lib_mdspan_at YYYYMML @_// also in_@ <mdspan>
> ```

:::

Adjust the placeholder value as needed so as to denote this proposal's date of adoption.

In 24.7.3.6.1 ([[mdspan.mdspan.overview]](https://eel.is/c++draft/mdspan.mdspan.overview)), add the following immediately after the subscript operators:

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

In 24.7.3.6.3 ([[mdspan.mdspan.members]](https://eel.is/c++draft/mdspan.mdspan.members)), add the following immediately after the subscript operators:

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

The `at()` member functions have been implemented in the `std::mdspan` reference implementation from the Kokkos project at Sandia National Laboratories [@kokkos/mdspan], see [@kokkos/mdspan#302].

---
references:
  - id: kokkos/mdspan
    citation-label: kokkos/mdspan
    title: "Reference implementation of mdspan"
    URL: https://github.com/kokkos/mdspan
  - id: kokkos/mdspan#302
    citation-label: kokkos/mdspan#302
    title: "Add element access via `at()` to `std::mdspan`"
    URL: https://github.com/kokkos/mdspan/pull/302
---
