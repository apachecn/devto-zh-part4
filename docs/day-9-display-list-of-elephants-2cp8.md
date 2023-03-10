# 第九天:展示大象清单

> 原文：<https://dev.to/rahulchowdhury/day-9-display-list-of-elephants-2cp8>

我最后添加了一个`RecyclerView`来显示 home 片段上的大象列表，这个片段就是`ElephantListFragment`。

有了`ListAdapter`、数据绑定和`DiffUtil`的 mocktail，以一种高效的方式将数据填充到`RecyclerView`就非常简单了。

这是我光滑的适配器:

```
class ElephantListAdapter : ListAdapter<Elephant, ElephantListAdapter.ViewHolder>(ElephantDiffCallback()) {
    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        val binding = ElephantListItemBinding.inflate(
            LayoutInflater.from(parent.context),
            parent,
            false
        )

        return ViewHolder(binding)
    }

    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        val elephant = getItem(position)
        holder.bind(elephant, createClickListener(elephantName = elephant.name))
    }

    class ViewHolder(
        private val elephantListItemBinding: ElephantListItemBinding
    ) : RecyclerView.ViewHolder(elephantListItemBinding.root) {

        fun bind(item: Elephant, listener: View.OnClickListener) {
            elephantListItemBinding.apply {
                elephant = item
                clickListener = listener
                executePendingBindings()
            }
        }
    }

    private fun createClickListener(elephantName: String): View.OnClickListener {
        return View.OnClickListener {
            val navArgs = Bundle()
            navArgs.putString("elephantName", elephantName)

            it.findNavController().navigate(R.id.elephantProfileFragment, navArgs)
        }
    }
}

private class ElephantDiffCallback : DiffUtil.ItemCallback<Elephant>() {
    override fun areItemsTheSame(oldItem: Elephant, newItem: Elephant): Boolean =
        oldItem.id == newItem.id

    override fun areContentsTheSame(oldItem: Elephant, newItem: Elephant): Boolean =
        oldItem == newItem
} 
```

抛光可以在这里和那里做，我会做一些时间。

查看回购，这里:[https://github.com/rahulchowdhury/elly](https://github.com/rahulchowdhury/elly)

接下来，我想用 Espresso 进行 UI 测试。有什么方便的指南给我吗？