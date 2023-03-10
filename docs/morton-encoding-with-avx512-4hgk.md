# 使用 AVX512 进行 Morton 编码

> 原文：<https://dev.to/wunk/morton-encoding-with-avx512-4hgk>

预计到 [Icelake 会将 AVX512 带到消费类硬件](https://en.wikipedia.org/wiki/AVX-512#CPUs_with_AVX-512)，我想出了一个替代`pdep`方法的方法来将 32 位 2D 坐标编码成 64 位莫顿代码，它利用了 AVX512 的 BITALG 子集的 [`vpshufbitqmb`指令](https://en.wikipedia.org/wiki/AVX-512#New_instructions_in_AVX-512_VPOPCNTDQ_and_AVX-512_BITALG)。
`vpshufbitqmb`允许通过索引到 64 位通道(到 avx 512`k`-寄存器掩码)来逐位构建新值。

它依赖于将两个 32 位的 X 和 Y 坐标解释为一个连续的 64 位值。它可以支持更高的维度，前提是 python- `zip`(在选择位之前，将坐标向量元素的大约前导字节输入并交织到每个 64 位通道中)输入坐标的特定字节。

目前没有该指令或任何硬件的吞吐量或延迟数据来实际测试这一点，但与目前最快的`pdep`方法相比，该组装看起来非常有前途。尤其是对于所有常量都在循环外加载的批量处理。

两种方法的示例:

```
#pragma pack(push, 1) union Coord2D
{
    std::uint32_t u32[2]; // x, y
    std::uint64_t u64;
};
#pragma pack(pop) 
std::uint64_t Morton2D_AVX512( const Coord2D& coord )
{
    const __m512i CoordVec = _mm512_set1_epi64(coord.u64);
    // Interleave bits from 32-bit X and Y coordinate
    const __mmask64 Interleave = _mm512_bitshuffle_epi64_mask(
        CoordVec,+
        _mm512_set_epi16(
            0x20'00 + 0x01'01 * 31, 0x20'00 + 0x01'01 * 30,
            0x20'00 + 0x01'01 * 29, 0x20'00 + 0x01'01 * 28,
            0x20'00 + 0x01'01 * 27, 0x20'00 + 0x01'01 * 26,
            0x20'00 + 0x01'01 * 25, 0x20'00 + 0x01'01 * 24,
            0x20'00 + 0x01'01 * 23, 0x20'00 + 0x01'01 * 22,
            0x20'00 + 0x01'01 * 21, 0x20'00 + 0x01'01 * 20,
            0x20'00 + 0x01'01 * 19, 0x20'00 + 0x01'01 * 18,
            0x20'00 + 0x01'01 * 17, 0x20'00 + 0x01'01 * 16,
            0x20'00 + 0x01'01 * 15, 0x20'00 + 0x01'01 * 14,
            0x20'00 + 0x01'01 * 13, 0x20'00 + 0x01'01 * 12,
            0x20'00 + 0x01'01 * 11, 0x20'00 + 0x01'01 * 10,
            0x20'00 + 0x01'01 *  9, 0x20'00 + 0x01'01 *  8,
            0x20'00 + 0x01'01 *  7, 0x20'00 + 0x01'01 *  6,
            0x20'00 + 0x01'01 *  5, 0x20'00 + 0x01'01 *  4,
            0x20'00 + 0x01'01 *  3, 0x20'00 + 0x01'01 *  2,
            0x20'00 + 0x01'01 *  1, 0x20'00 + 0x01'01 *  0
        )
    );
    return _cvtmask64_u64(Interleave);
}

std::uint64_t Morton2D_BMI(const Coord2D& coord)
{
    return _pdep_u64(static_cast<std::uint64_t>(coord.u32[0]), 0x5555555555555555)
        | _pdep_u64(static_cast<std::uint64_t>(coord.u32[1]), 0xAAAAAAAAAAAAAAAA);
} 
```

使用`gcc 9.1`与`-Ofast -march=icelake-client`生成的装配:

```
Morton2D_AVX512(Coord2D const&):
        vpbroadcastq    zmm0, QWORD PTR [rdi]
        vpshufbitqmb    k0, zmm0, ZMMWORD PTR .LC0[rip]
        kmovq   rax, k0
        vzeroupper
        ret
Morton2D_BMI(Coord2D const&):
        mov     eax, DWORD PTR [rdi]
        movabs  rdx, 6148914691236517205
        pdep    rax, rax, rdx
        mov     edx, DWORD PTR [rdi+4]
        movabs  rcx, -6148914691236517206
        pdep    rdx, rdx, rcx
        or      rax, rdx
        ret 
```

我已经用[英特尔的仿真器](https://software.intel.com/en-us/articles/intel-software-development-emulator)验证了实现，一旦我拿到一些 Icelake 硬件，我很乐意用一些实际的基准更新。