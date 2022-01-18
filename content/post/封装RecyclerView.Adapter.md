---
title: "封装RecyclerView.Adapter"
date: 2022-01-18T14:11:54+08:00
draft: false
---
打造一个通用的Adapter模版，避免写Adapter中的大量重复代码。

通过组装的方式来构建Adapter，这样做的好处就是减少耦合，去掉一种item 或者添加一种item对于列表是没有任何影响的。

符合高内聚，低耦合，扩展方便。

<!--more-->

### BaseRecyclerViewHolder

```kotlin
abstract class BaseRecyclerViewHolder<T>(itemView: View) : RecyclerView.ViewHolder(itemView) {
    abstract fun bind(data: T, payload: Any?)
    open fun onViewAttachedToWindow() {}
    open fun onViewDetachToWindow() {}
    open fun onViewRecycled() {}
}
```

### BaseRecyclerViewAdapter

```kotlin
abstract class BaseRecyclerViewAdapter<T> : RecyclerView.Adapter<BaseRecyclerViewHolder<T>>() {
    
    private val dataSet: MutableList<T> = mutableListOf()
    
    fun getDataList() = dataSet
    
    fun getItem(index: Int): T? {
        if (!containIndex(index)) {
            return null
        }
        return dataSet[index]
    }

    open fun add(element: T?) {
        add(dataSet.size, element)
    }

    open fun add(index: Int, element: T?) {
        if (!containIndex(index) || null == element) {
            return
        }
        dataSet.add(index, element)
        notifyItemInserted(index)
    }

    open fun addAll(elements: List<T>?) {
        addAll(dataSet.size, elements)
    }

    open fun addAll(index: Int, elements: List<T>?) {
        if (!containIndex(index) || elements.isNullOrEmpty()) {
            return
        }
        dataSet.addAll(index, elements)
        notifyItemRangeInserted(index, elements.size)
    }

    open fun update(index: Int) {
        if (!containIndex(index)) {
            return
        }
        notifyItemChanged(index)
    }

    open fun update(index: Int, element: T?) {
        update(index, element, null)
    }

    open fun update(index: Int, element: T?, payload: Any?) {
        if (!containIndex(index) || null == element) {
            return
        }
        dataSet[index] = element
        notifyItemChanged(index, payload)
    }

    open fun remove(index: Int): T? {
        if (!containIndex(index)) {
            return null
        }
        val element = dataSet.removeAt(index)
        notifyItemRemoved(index)
        return element
    }

    open fun submitList(elements: List<T>?, callback: DiffUtil.Callback? = null) {
        if (elements.isNullOrEmpty()) {
            val size = dataSet.size
            if (size > 0) {
                dataSet.clear()
                notifyItemRangeRemoved(0, size)
            }
        } else {
            var diffCallback = callback
            if (null == diffCallback) {
                diffCallback = DefaultItemCallback(dataSet, elements)
            }
            val result = DiffUtil.calculateDiff(diffCallback)
            dataSet.clear()
            dataSet.addAll(elements)
            result.dispatchUpdatesTo(this)
        }
    }

    override fun getItemCount(): Int {
        return dataSet.size
    }

    override fun onBindViewHolder(holder: BaseRecyclerViewHolder<T>, position: Int) {
        val item = getItem(position) ?: return
        holder.bind(item, null)
    }

    override fun onBindViewHolder(holder: BaseRecyclerViewHolder<T>, position: Int, payloads: MutableList<Any>) {
        if (payloads.isNotEmpty()) {
            val item = getItem(position) ?: return
            holder.bind(item, payloads[0])
        } else {
            super.onBindViewHolder(holder, position, payloads)
        }
    }

    private fun containIndex(index: Int): Boolean {
        return index >= 0 && index < dataSet.size
    }

    private class DefaultItemCallback<T>(private val oldList: List<T>, private val newList: List<T>) : DiffUtil.Callback() {

        override fun getOldListSize(): Int {
            return oldList.size
        }

        override fun getNewListSize(): Int {
            return newList.size
        }

        override fun areItemsTheSame(oldItemPosition: Int, newItemPosition: Int): Boolean {
            return oldList[oldItemPosition] == newList[newItemPosition]
        }

        override fun areContentsTheSame(oldItemPosition: Int, newItemPosition: Int): Boolean {
            return oldList[oldItemPosition] == newList[newItemPosition]
        }

    }
}
```

### MultiItemType

```kotlin
interface MultiItemType {
    fun getItemType(): Int
}
```

### MultiViewHolderProvider

```kotlin
interface MultiViewHolderProvider<T : MultiItemType> {
    fun create(parent: ViewGroup, viewType: Int): BaseRecyclerViewHolder<T>
}
```

### MultiItemViewAdapter

```kotlin
open class MultiItemViewAdapter<T : MultiItemType>(private val provider: MultiViewHolderProvider<T>) : BaseRecyclerViewAdapter<T>() {

    override fun getItemViewType(position: Int): Int {
        val item = getItem(position)
        if (null != item) {
            return item.getItemType()
        }
        return super.getItemViewType(position)
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): BaseRecyclerViewHolder<T> {
        return provider.create(parent, viewType)
    }
}
```

