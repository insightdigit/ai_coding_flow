# 前端開發規範

---

## 1. Blade 模板規範

### 1.1 模板結構

```blade
{{-- ✅ 正確：清晰的模板結構 --}}
@extends('layouts.app')

@section('title', '產品列表')

@section('content')
<div class="container mx-auto px-4 py-8">
    {{-- 頁面標題 --}}
    <h1 class="text-3xl font-bold mb-6">產品管理</h1>

    {{-- 搜尋表單 --}}
    @include('products.partials.search-form')

    {{-- 產品列表 --}}
    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
        @forelse($products as $product)
            {{-- 產品卡片元件 --}}
            <x-product-card :product="$product" />
        @empty
            {{-- 空狀態 --}}
            <div class="col-span-full text-center py-12">
                <p class="text-gray-500">尚無產品資料</p>
            </div>
        @endforelse
    </div>

    {{-- 分頁 --}}
    <div class="mt-8">
        {{ $products->links() }}
    </div>
</div>
@endsection

@push('scripts')
<script>
    // 頁面專用的 JavaScript
</script>
@endpush
```

### 1.2 Blade Components

```blade
{{-- resources/views/components/product-card.blade.php --}}
@props(['product'])

<div class="bg-white rounded-lg shadow-md p-6 hover:shadow-lg transition-shadow">
    {{-- 產品圖片 --}}
    <img src="{{ $product->image_url }}"
         alt="{{ $product->name }}"
         class="w-full h-48 object-cover rounded-md mb-4">

    {{-- 產品資訊 --}}
    <h3 class="text-xl font-semibold mb-2">{{ $product->name }}</h3>
    <p class="text-gray-600 mb-4">{{ Str::limit($product->description, 100) }}</p>

    {{-- 操作按鈕 --}}
    <div class="flex justify-between items-center">
        <span class="text-sm text-gray-500">{{ $product->category->name }}</span>
        <a href="{{ route('products.show', $product->id) }}"
           class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600">
            查看詳情
        </a>
    </div>
</div>
```

**使用方式:**
```blade
{{-- 使用 Component --}}
<x-product-card :product="$product" />

{{-- 傳遞多個屬性 --}}
<x-data-table
    :columns="$columns"
    :data="$data"
    :sortable="true"
    class="mt-4"
/>
```

---

## 2. Tailwind CSS 規範

```blade
{{-- ✅ 正確：使用 Tailwind 工具類別 --}}
<div class="container mx-auto px-4 py-8">
    <h1 class="text-3xl font-bold text-gray-800 mb-6">標題</h1>
    <p class="text-base text-gray-600 leading-relaxed">內容</p>

    <button class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600
                   focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-opacity-50
                   transition-colors duration-200">
        按鈕
    </button>
</div>

{{-- ✅ 響應式設計 --}}
<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
    {{-- 手機: 1 列, 平板: 2 列, 桌機: 3 列 --}}
</div>

{{-- ❌ 禁止：內嵌樣式 --}}
<div style="padding: 20px; margin: 10px;">  {{-- 不要這樣做 --}}
    內容
</div>

{{-- ❌ 禁止：自訂 CSS（除非必要） --}}
<div class="custom-style">  {{-- 盡量避免 --}}
    內容
</div>
```

**Tailwind 最佳實踐:**
- ✅ 使用 Tailwind 工具類別
- ✅ 使用響應式前綴 (sm:, md:, lg:, xl:)
- ✅ 使用 hover:, focus:, active: 等狀態前綴
- ✅ 複雜的樣式抽取為 Component
- ❌ 避免內嵌 style 屬性
- ❌ 避免自訂 CSS 類別

---

## 3. JavaScript 整合

```blade
{{-- ✅ 使用 Alpine.js（輕量級互動） --}}
<div x-data="{ open: false }">
    {{-- 按鈕 --}}
    <button @click="open = !open" class="px-4 py-2 bg-blue-500 text-white rounded">
        切換
    </button>

    {{-- 內容 --}}
    <div x-show="open" x-transition class="mt-4 p-4 bg-gray-100 rounded">
        可切換的內容
    </div>
</div>

{{-- ✅ 使用 @push 載入頁面專用的 JS --}}
@push('scripts')
<script>
    // 頁面專用的 JavaScript
    document.addEventListener('DOMContentLoaded', function() {
        // 初始化
    });
</script>
@endpush
```

---

## 4. 表單處理

```blade
{{-- ✅ 正確：完整的表單處理 --}}
<form method="POST" action="{{ route('products.store') }}" class="space-y-6">
    @csrf

    {{-- 產品名稱 --}}
    <div>
        <label for="name" class="block text-sm font-medium text-gray-700 mb-2">
            產品名稱 <span class="text-red-500">*</span>
        </label>
        <input type="text"
               id="name"
               name="name"
               value="{{ old('name') }}"
               required
               class="w-full px-4 py-2 border border-gray-300 rounded-md
                      focus:ring-2 focus:ring-blue-500 focus:border-blue-500
                      @error('name') border-red-500 @enderror">

        @error('name')
            <p class="mt-1 text-sm text-red-500">{{ $message }}</p>
        @enderror
    </div>

    {{-- 分類選擇 --}}
    <div>
        <label for="category_id" class="block text-sm font-medium text-gray-700 mb-2">
            分類 <span class="text-red-500">*</span>
        </label>
        <select id="category_id"
                name="category_id"
                required
                class="w-full px-4 py-2 border border-gray-300 rounded-md">
            <option value="">請選擇分類</option>
            @foreach($categories as $category)
                <option value="{{ $category->id }}"
                        {{ old('category_id') == $category->id ? 'selected' : '' }}>
                    {{ $category->name }}
                </option>
            @endforeach
        </select>

        @error('category_id')
            <p class="mt-1 text-sm text-red-500">{{ $message }}</p>
        @enderror
    </div>

    {{-- 送出按鈕 --}}
    <div class="flex justify-end space-x-4">
        <a href="{{ route('products.index') }}"
           class="px-6 py-2 border border-gray-300 rounded-md hover:bg-gray-50">
            取消
        </a>
        <button type="submit"
                class="px-6 py-2 bg-blue-500 text-white rounded-md hover:bg-blue-600">
            建立產品
        </button>
    </div>
</form>

{{-- 顯示成功訊息 --}}
@if(session('success'))
    <div class="mb-4 p-4 bg-green-100 border border-green-400 text-green-700 rounded">
        {{ session('success') }}
    </div>
@endif

{{-- 顯示錯誤訊息 --}}
@if(session('error'))
    <div class="mb-4 p-4 bg-red-100 border border-red-400 text-red-700 rounded">
        {{ session('error') }}
    </div>
@endif
```
