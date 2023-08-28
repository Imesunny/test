##json Server Manipulation

import './App.css';
import {useState,UseEffect, useEffect} from "react";


const initialState = {
  title: "",
  price: "",
  category:"",
  image:""
}

function App() {
  const[formData, setFormData]=useState(initialState);
  const[data,setData]=useState([]);
  const[page,setPage]=useState(1);
  const[filterby,setFilterBy]=useState("");
  const[OrderBy,setOrderBy]=useState([]);
  const[loading,setLoading]=useState(false);
  const limit=4;
  const sort="price";

  const getApiUrl=(url,filterby,OrderBy,sort)=>{
    if (filterby){
      return (url=`${url}&category=${filterby}`);
    }
    else if(OrderBy && sort){
      return(url=`${url}&_sort=${sort}&_order=${OrderBy}`);
    }
    return url;
  }

  let apiUrl=getApiUrl(`http://localhost:3000/products?_page=${page}&_limit=${limit}`, filterby, OrderBy,sort);

  //to get the data from db.json(GET REQUEST)-making fetch request
  const getProductDatafromAPI=async (page)=>{
    setLoading(true);
    try {
     const res=await fetch(apiUrl);
     const data=await res.json();
     setData(data);
    //  console.log(data);     
    } catch (error) {
      console.log(error);
      setLoading(false);
    }
  };

  useEffect(()=>{
    getProductDatafromAPI(page);
  },[page,filterby,OrderBy,sort]);


  // console.log(initialState)

  //to get the values from the form(change Event)
  const handleChange=(e)=>{
    // console.log(e);
    setFormData({...formData,[e.target.name]: e.target.value})
    // console.log(formData);
  };

  const handleFormSubmit=(e)=>{
    e.preventDefault();
    // console.log(e);
    setLoading(true);
    fetch("http://localhost:3000/products",{
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify(formData),
    })
    .then((res)=>res.json())
    .then((data)=>{
      setLoading(false);
      // setFormData("");
      getProductDatafromAPI();
    })
    .catch((error)=>{
      setLoading(false);
      console.log(error);    
    })
  };


  return (
    <div className="App">
    <div>
        <h1>Form Input</h1>

        <form action="" onSubmit={handleFormSubmit}>

        <label>
          Title:<input type="text"name= "title" onChange={handleChange}/>
        </label> <br></br><br></br>

        <label>
          Price:<input type="text"name= "price" onChange={handleChange}/>
        </label><br></br><br></br>

        <label>
          Category:<input type="text"name= "category" onChange={handleChange}/>
        </label><br></br><br></br>

        <label>
          Image:<input type="text"name= "image"onChange={handleChange}/>
        </label><br></br><br></br>

        <button type="submit">Submit</button>
          
        </form>
        </div>

        <div>
          <h1>Filtering</h1>
          <input type="radio" name="category" values="category" checked={filterby==="electronics"}
            onChange={()=>{
              setFilterBy("electronics")
              console.log(filterby);
              }}
          />
          <label>Filter by Electronics</label>

          <input type="radio" name="category" values="category" checked={filterby==="women's clothing"}
            onChange={()=>{
              setFilterBy("women's clothing")
              console.log(filterby);
              }}
          />
          <label>Filter by women's clothing</label>

          <input type="radio" name="category" values="category" checked={filterby==="men's clothing"}
            onChange={()=>{
              setFilterBy("men's clothing")
              console.log(filterby);
              }}
          />
          <label>Filter by men's clothing</label>

          <input type="radio" name="category" values="category" checked={filterby==="jewelery"}
            onChange={()=>{
              setFilterBy("jewelery")
              console.log(filterby);
              }}
          />
          <label>Filter by jewelery</label>
          <button onClick={()=>{
            setFilterBy("");
          }}>
          Reset
          </button>
        </div>

        <div>
          <button onClick={()=>{
            setOrderBy("asc")
            // console.log(sortBy);
          }}>Ascending</button>

          <button onClick={()=>{
            setOrderBy("desc")
          }}>Descending</button>

          <button onClick={()=>{
            setOrderBy("")
          }}>Reset All</button>
        </div>

       <div>
        <h1>Product Data</h1>
        <div style={{display:"grid", gridTemplateColumns:"repeat(2,1fr)"}}>
          {
           data.map((e)=>(
            <div key={e.id}>
              <img src={e.image} alt="" width={200} height={200}/>
              <p>Title:{e.title}</p>
              <p>Category:{e.category}</p>
              <p>Price:{e.price}</p>
            </div>
           )) 
          }
        </div>
        <div>
          <button onClick={()=>{
            setPage(page-1)
          console.log(page);
          }}
          disabled={page===1}
          >
          Prev
          </button>

          <button >{page}</button>

          <button onClick={()=>{
            setPage(page+1)
          console.log(page);
          }}
          // disabled={page===1}
          >
          Next
          </button>

        </div>
       </div>
    





    </div>
  );
}

export default App;
