---
title: mdspan.at()
document: P3383R1
date: today
audience:
- Library Evolution Working Group (LEWG)
- SG23 Safety and Security
author:
  - name: Stephan Lachnit
    email: <stephan.lachnit@cern.ch>
  - name: Xavier Bonaventura ([BMW](https://www.bmwgroup.com/en/innovation/automated-driving.html))
    email: <xavier.bonaventura@bmw.de>
toc: true
---

# Introduction

This paper proposes element access with bounds checking to `std::mdspan` via `at()` member functions.

# Revision history

## R1

Update wording to align with [@P2821R5]:

- Bump `__cpp_lib_mdspan` instead of adding `__cpp_lib_mdspan_at`
- Mark `at()` as freestanding deleted and adjust freestanding comments of header and class

## R0

Initial version

# Motivation

## Safety

The new `at()` member functions provide memory-safe element access to `std::mdspan`, and thus have defined behavior. Out-of-bound access can be caught by catching the `std::out_of_range` exception.

## Consistency

In [@P2821R5], element access with bounds checking via `at()` has been added to `std::span`. One of the main motivations for this change was consistency with other containers that have element access with bounds checking via `at()`. Similarly, such element access should be added to `std::mdspan`.

# Impact On the Standard

The impact of this proposal on the standard is low. The proposed function signatures for the `at()` member functions are identical to the function signatures for the subscript operators as proposed in [@P0009R18].

One consideration is that the `at()` method has previously not been used with multi-dimensional arguments. However, this was also true for the subscript operator  before the possibility was introduced in [@P2128R6].

# Wording

The wording is relative to [@N4993].

In 17.3.2 ([[version.syn]](https://eel.is/c++draft/version.syn)), adjust the value of `__cpp_lib_mdspan` to the date of this proposal's adoption.

In 23.7.3.2 ([[mdspan.syn]](https://eel.is/c++draft/mdspan.syn)), change the following lines:

```diff
- // all freestanding
+ // mostly freestanding
namespace std {
```

```diff
// 23.7.3.6, class template mdspan
template<class ElementType, class Extents, class LayoutPolicy = layout_right,
         class AccessorPolicy = default_accessor<ElementType>>
-  class mdspan;
+  class mdspan;                                                                // partially freestanding
```

In 23.7.3.6.1 ([[mdspan.mdspan.overview]](https://eel.is/c++draft/mdspan.mdspan.overview)), add the following immediately after the subscript operators:

::: add

> ```
> template<class... OtherIndexTypes>
>   constexpr reference at(OtherIndexTypes... indices) const;   // freestanding-deleted
> template<class OtherIndexType>
>   constexpr reference at(span<OtherIndexType, rank()> indices) const;   // freestanding-deleted
> template<class OtherIndexType>
>   constexpr reference at(const array<OtherIndexType, rank()>& indices) const;   // freestanding-deleted
> ```

:::

In 23.7.3.6.3 ([[mdspan.mdspan.members]](https://eel.is/c++draft/mdspan.mdspan.members)), add the following immediately after the subscript operators:

::: add

> ```
> template<class... OtherIndexTypes>
>   constexpr reference at(OtherIndexTypes... indices) const;
> ```
> [7]{.pnum} *Constraints:*
>
> - [7.1]{.pnum} `(is_convertible_v<OtherIndexTypes, index_type> && ...)` is `true`,
> - [7.2]{.pnum} `(is_nothrow_constructible_v<index_type, OtherIndexTypes> && ...)` is `true`, and
> - [7.3]{.pnum} `sizeof...(OtherIndexTypes) == rank()` is `true`.
>
> [8]{.pnum} *Returns:* `(*this)[indices...]`
>
> [9]{.pnum} *Throws:* `out_of_range` if\
> `indices_v[i] >= extent(i) || indices_v[i] < 0`\
> for any `indices_v[i]` in `vector<OtherIndexTypes>({indices...})`.
>
> ```
> template<class OtherIndexType>
>   constexpr reference at(span<OtherIndexType, rank()> indices) const;
> ```
> ```
> template<class OtherIndexType>
>   constexpr reference at(const array<OtherIndexType, rank()>& indices) const;
> ```
> [10]{.pnum} *Constraints:*
>
> - [10.1]{.pnum} `is_convertible_v<const OtherIndexType&, index_type>` is `true`, and
> - [10.2]{.pnum} `is_nothrow_constructible_v<index_type, const OtherIndexType&>` is `true`.
>
> [11]{.pnum} *Returns:* `(*this)[indices]`
>
> [12]{.pnum} *Throws:* `out_of_range` if\
> `indices[i] >= extent(i) || indices[i] < 0`\
> for any `indices[i]` in `indices`.

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
