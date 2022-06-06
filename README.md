# SearchApiFiltering

function 
  binding.searchView.setOnQueryTextListener(object : SearchView.OnQueryTextListener {
            override fun onQueryTextSubmit(query: String): Boolean {
                getAllLibraryApiCalling(
                    sharedpreference!!.getCity(),
                    sharedpreference!!.getLat(),
                    sharedpreference!!.getLng()
                )
                return false
            }

            override fun onQueryTextChange(newText: String): Boolean {

                searchAdapter?.filter?.filter(newText)


                return true
            }

        })
        
        
        
        
        
        
        --------------------------------------------------------------------------------------------------------------------------------------
        
        
        
        
        
        //search adapter
        class SearchAdpter(

    val context: Context,
    var data: List<Data>
    ,var call:callback

) :
    RecyclerView.Adapter<SearchAdpter.LibrarylistRecyclerViewHolder>(), Filterable {

    var result_itemList = ArrayList<Data>()
    var callingone_time=false
    // exampleListFull . exampleList

    init {
        result_itemList = data as ArrayList<Data>
    }


   inner class LibrarylistRecyclerViewHolder(
        private val binding: ItemSearchBinding
    ) :
        RecyclerView.ViewHolder(binding.root) {

        @SuppressLint("SetTextI18n")
        fun OnBind(library: Data) {

            binding.carview.setOnClickListener{
                val bundle = Bundle()
                bundle.putString("library_id", library.library_id)
                bundle.putString("library_name", library.library_name)
                bundle.putString("library_distance", library.distance_from_user)
                binding.carview.setOnClickListener{
                    Navigation.findNavController(binding.root)
                        .navigate(R.id.actionnavsearch_to_nav_library_study,bundle)
                }
            }
            Glide.with(context)
                .load(library.library_image1)
                .centerInside()
                .diskCacheStrategy(DiskCacheStrategy.ALL)
                .placeholder(R.drawable.ic_baseline_library_books_24)
                .into(binding.imageview)

            binding.libraryname.text = library.library_name
            binding.address.text = "${library.address_line}"
            binding.charge.text = library.minimum_price

            binding.rating.numStars=5
            binding.rating.stepSize = 0.25F;
            binding.rating.setIsIndicator(true);
            binding.rating.rating = library.ratings.toFloat();



//            binding.distanceFromUser.text="${(library.distance_from_user.toInt()/1000)} km"
//            binding.rating.numStars= library.ratings.toInt()
//            binding.libraryPrice.text=library.morning_slot_price
//            binding.txtRate.text=library.ratings.toString()
//            binding.rating.numStars=5
//            binding.rating.stepSize = 0.25F;
//            binding.rating.setIsIndicator(true);
//            binding.rating.setRating(library.ratings.toFloat());

        }
    }

    override fun onCreateViewHolder(
        parent: ViewGroup,
        viewType: Int
    ): LibrarylistRecyclerViewHolder {
        val binding = ItemSearchBinding.inflate(LayoutInflater.from(parent.context))
        return LibrarylistRecyclerViewHolder(binding)
    }

    override fun onBindViewHolder(holder: LibrarylistRecyclerViewHolder, position: Int) {

            holder.OnBind(result_itemList?.get(position) !!)

    }

    override fun getItemCount(): Int {
        if(callingone_time) {
            call.callingSize(result_itemList!!.size)
            return result_itemList!!.size
        }
        else
            return 0


    }
//    fun notifyDataChanged1(itemList1: List<DataX>){
//        itemList= itemList1
//        notifyDataSetChanged()
//    }

    override fun getFilter(): Filter {
        return object : Filter() {
            override fun performFiltering(constraint: CharSequence?): FilterResults {
                val charSearch = constraint.toString()

                if (charSearch.isEmpty()) {
                    callingone_time=false
                    result_itemList = data as ArrayList<Data>
                } else {
                    callingone_time=true
                    val resultList = ArrayList<Data>()
                    for (row in data!!) {
                        if (row.library_name.lowercase().contains(constraint.toString().lowercase())) {
                            resultList.add(row)
                        }
                    }
                    result_itemList = resultList
                }
                val filterResults = FilterResults()
                filterResults.values = result_itemList
                return filterResults
            }

            override fun publishResults(constraint: CharSequence?, results: FilterResults?) {
                result_itemList = results?.values as ArrayList<Data>
                notifyDataSetChanged()
            }
        }
    }
interface callback{
    fun callingSize(b:Int)
}
