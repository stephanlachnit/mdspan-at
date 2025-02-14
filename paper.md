---
title: mdspan.at()
document: P3383R2
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

## R2

- Remove feature test macro bump requested by LEWG
- Adapt wording to follow same pattern like `operator[]` and rebase on top of latest draft

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

The wording is relative to [@N5001].

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
> [8]{.pnum} *Returns:* `(*this)[indices...]`.
>
> [9]{.pnum} Let `I` be `extents_type::`*`index_cast`*`(std::move(indices))`.
>
> [10]{.pnum} *Throws:* `out_of_range` if `I` is not a multidimensional index in `extends()`.
>
> ```
> template<class OtherIndexType>
>   constexpr reference at(span<OtherIndexType, rank()> indices) const;
> ```
> ```
> template<class OtherIndexType>
>   constexpr reference at(const array<OtherIndexType, rank()>& indices) const;
> ```
> [11]{.pnum} *Constraints:*
>
> - [11.1]{.pnum} `is_convertible_v<const OtherIndexType&, index_type>` is `true`, and
> - [11.2]{.pnum} `is_nothrow_constructible_v<index_type, const OtherIndexType&>` is `true`.
>
> [12]{.pnum} *Effects:* Let `P` be a parameter pack such that
>   `is_same_v<make_index_sequence<rank()>, index_sequence<P...>>`
> is `true`. Equivalent to:
>   `return at(extents_type::`*`index-cast`*`(as_const(indices[P]))...);`

:::

# Feature Test Macro

LEWG decided to drop the bump of the feature test macro.

## Poll

Remove the feature test macro bump from P3383R1

| SF | F | N | A | SA |
|----|---|---|---|----|
|  4 | 4 | 4 | 2 |  0 |

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
