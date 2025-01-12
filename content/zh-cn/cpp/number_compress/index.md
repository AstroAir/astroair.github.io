---
title: 数字压缩技术在 C++ 中的应用
---

在 C++ 中，数字压缩技术可以被用来减少存储浮点数（如 `float` 或 `double`）所需的内存空间。这种压缩技术特别适用于数据量庞大的应用，如图像处理、科学计算、机器学习等。在这些场合，压缩可以帮助节省存储空间并加快数据传输速度。

---

## 常见的压缩浮点数字类型的方法

1. **量化（Quantization）**
2. **浮点数的位域表示（Bit-Packing）**
3. **熵编码（Entropy Coding）**
4. **差分编码（Differential Encoding）**
5. **使用更小的数值类型（Reduced Precision）**

---

## 1. 量化（Quantization）

量化是将连续值映射到有限的离散值集合，例如将 `float` 或 `double` 值缩减到 `int` 或 `short`。量化可减少数据的存储精度，但保留可接受的数据范围。

```cpp
#include <iostream>
#include <vector>
#include <cmath>

std::vector<int> quantize(const std::vector<float>& floats, float scaleFactor) {
    std::vector<int> quantized;
    for (float number : floats) {
        quantized.push_back(static_cast<int>(std::round(number * scaleFactor)));
    }
    return quantized;
}

int main() {
    std::vector<float> numbers = {0.1f, 0.5f, 0.9f};
    float scaleFactor = 100.0f; // 将浮点数放大100倍

    auto quantized = quantize(numbers, scaleFactor);

    for (int q : quantized) {
        std::cout << q << " "; // 输出量化后的结果，如 10, 50, 90
    }
    return 0;
}
```

---

## 2. 浮点数的位域表示（Bit-Packing）

通过位域，可以手动压缩浮点数的存储，尤其是使用更小的精度或自定义格式。例如，使用 16 位浮点数（half-precision）。

```cpp
#include <iostream>
#include <cstdint>

struct HalfFloat {
    uint16_t value; // 16位表示
};

// 将32位浮点数转为16位半浮点
HalfFloat floatToHalf(float f) {
    HalfFloat h;
    uint32_t floatBits = *reinterpret_cast<uint32_t*>(&f);
    uint32_t sign = (floatBits >> 16) & 0x8000; // 提取符号位
    uint32_t exponent = ((floatBits >> 23) & 0xFF) - 112; // 转为偏移量为15的指数
    uint32_t mantissa = floatBits & 0x7FFFFF; // 提取尾数

    if (exponent <= 0) {
        h.value = sign; // 0 或 渐近于0
    } else if (exponent >= 31) {
        h.value = sign | 0x7FFF; // +inf 或 -inf
    } else {
        h.value = sign | (exponent << 10) | (mantissa >> 13);
    }

    return h;
}

int main() {
    float num = 3.14f;
    HalfFloat half = floatToHalf(num);
    std::cout << std::hex << half.value << std::endl; // 输出16位半浮点的十六进制表示
    return 0;
}
```

---

## 3. 熵编码（Entropy Coding）

在此方法中，可以对浮点数序列进行统计分析，然后使用更短的编码表示频繁出现的值。常用的熵编码方法有 Huffman 编码和算术编码。

```cpp
#include <iostream>
#include <unordered_map>
#include <vector>
#include <queue>

// 假设编码表为简单固定长度的哈夫曼编码
std::unordered_map<float, std::string> buildHuffmanTable(const std::vector<float>& data) {
    std::unordered_map<float, int> frequency;
    for (float f : data) {
        frequency[f]++;
    }

    // 此处省略实际哈夫曼树构建和编码生成，只用数量替代
    std::unordered_map<float, std::string> huffmanTable;
    for (const auto& pair : frequency) {
        huffmanTable[pair.first] = std::to_string(pair.first); // Dummy encoding
    }

    return huffmanTable;
}

int main() {
    std::vector<float> numbers = {1.0f, 2.0f, 1.0f, 3.5f, 2.0f};
    auto huffmanTable = buildHuffmanTable(numbers);

    for (const auto& pair : huffmanTable) {
        std::cout << pair.first << ": " << pair.second << std::endl; // 输出伪编码
    }
    return 0;
}
```

---

## 4. 差分编码（Differential Encoding）

在差分编码中，存储相邻浮点数之间的差值，而非实际值，这在数据变化很小的情况下尤其高效。由于相邻的数据通常很相似，存储较小的差值可以显著减少数据量。

```cpp
#include <iostream>
#include <vector>

std::vector<float> deltaEncode(const std::vector<float>& data) {
    std::vector<float> encoded;
    if (data.empty()) return encoded;

    encoded.push_back(data[0]); // 存储第一个值
    for (size_t i = 1; i < data.size(); ++i) {
        encoded.push_back(data[i] - data[i - 1]); // 存储差值
    }
    return encoded;
}

int main() {
    std::vector<float> numbers = {1.0f, 1.1f, 1.2f, 1.1f, 1.0f};
    auto encoded = deltaEncode(numbers);

    for (float val : encoded) {
        std::cout << val << " "; // 输出编码结果
    }
    return 0;
}
```

---

## 5. 使用更小的数值类型（Reduced Precision）

如果对精度要求不高，可以使用 `float` 替代 `double`。同样，也可以自定义小数点位置，简化存储格式。

```cpp
#include <iostream>
#include <vector>

std::vector<uint16_t> compressFloatToHalf(const std::vector<float>& data) {
    std::vector<uint16_t> compressed;
    for (float num : data) {
        uint16_t half = static_cast<uint16_t>(num * 100); // 假设放缩以提高精度
        compressed.push_back(half);
    }
    return compressed;
}

int main() {
    std::vector<float> numbers = {1.23f, 4.56f, 7.89f};
    auto compressed = compressFloatToHalf(numbers);

    for (uint16_t val : compressed) {
        std::cout << val << " "; // 输出压缩结果
    }
    return 0;
}
```

---

## 总结

以上方法展示了在 C++ 中对浮点数（`float` 和 `double`）进行压缩的几种技术。不同的场景需要选择合适的方法。确保尽量减少误差的方法包括选择合适的数据类型、使用量化及差分编码、执行熵编码等。这将有助于在尽量减少存储空间的同时保持数据的有效性和准确性。
