---
title: mdspan.at()
document: PnnnnR0
date: 2023-11-28
audience:
- Library Evolution Working Group (LEWG)
- SG23 Safety and Security
author:
  - name: Stephan Lachnit
    email: <stephanlachnit@debian.org>
toc: true
---

# Introduction

`std::mdspan::at` element access with boundary checking as alternative to element access via `std::mdspan::operator[]`.

# Motivation

## Safety

Provide memory safe access to mdspan elements.

## Consistency

Consistency with `std::span::at` [@P2821R4] and other containers.

# Impact On the Standard

Low impact:

- Design similar to [@P2821R4] and [@P0009R18]
- Signature identical to multi-dimensional `operator[]` in [@P0009R18]
- While `.at()` previously not used with multi-dimensional arguments, this is also true for the multi-dimensional `operator[]` before it was introduced in [@P2128R6]

# Wording

Relative to [@P0009R18], insert:

```c++
template< class... OtherIndexTypes >
constexpr reference at( OtherIndexTypes... indices ) const;

template< class OtherIndexType >
constexpr reference at( std::span<OtherIndexType, rank()> indices ) const;

template< class OtherIndexType >
constexpr reference at( const std::array<OtherIndexType, rank()>& indices ) const;
```

# Feature test macro

```c++
#define __cpp_lib_mdspan_at 20XXXXL // also in <mdspan>
```

# Reference Implementation

See [@Implementation]

---
references:
  - id: Implementation
    citation-label: Implementation
    URL: https://github.com/kokkos/mdspan/pull/302
---
