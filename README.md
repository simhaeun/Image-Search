# Image-Search
[![Netlify Status](https://api.netlify.com/api/v1/badges/b59a1884-f614-465c-9ee6-24ec989adbf7/deploy-status)](https://app.netlify.com/sites/hacookie-image-search/deploys)

## 이미지 검색 서비스
![image](https://user-images.githubusercontent.com/58839497/211521059-e606310e-59bd-40d9-8f5f-aab8f1918918.png)

### unsplash API 

- 검색 결과의 뷰 처리는 Infinity Scroll 을 적용시켰습니다.
```jsx
import { useEffect, useState } from "react"

// 보이는지 안보이는지 알수있도록 ref를 받음 
export default function useIntersectionObserver(ref) {
  const [isIntersecting, setIsIntersecting] = useState(false)

  useEffect(() => {
    if (!ref.current) return;
    // IO(IntersectionObserver)정의. 대상을 entry 하나만으로 정함
    const IO = new IntersectionObserver(([entry]) => {
      setIsIntersecting(entry.isIntersecting)
    })

    IO.observe(ref.current); // IO에 observe할 대상을 추가

    return () => { // 클린업 함수
      IO.disconnect()
    }
  },[ref]) // ref 값이 바뀔때마다 useEffect 실행

  return isIntersecting
}
```

- 로딩
```jsx
import { InfinitySpin } from 'react-loader-spinner';

const ref = useRef() // 관찰할 대상

return (
	<div ref={ref} className='loading'>
		// 로딩 중이라면 로딩 아이콘 노출
    {isLoading ? ( 
      <InfinitySpin width='200' color="#4fa94d" />
    ) : isEnd && !isEmpty ? ( // 로딩 끝, 값이 없거나, 전체 데이터로드가 완료된 상태라면
      <p>- - No More Images - -</p> 
    ) : null}
  </div>
)
```
- 검색 결과 없을 시
```jsx
const photos = data?.map(item => item.results).flat() ?? []
const isEmpty = data?.[0]?.results === 0;
const isEnd = photos.length === data?.[0]?.total;
const isLoading = (!data && !error && query) || isValidating

return (
	{isEmpty ? ( <div className='no-result'> // 검색 결과가 없다면
    <FaRegGrinBeamSweat className='no-result-icon'/>No Images Found</div> 
	) : null}

	<div className='image-container'>
    {photos.map(({urls, alt_description}, index) => (
      <img key={index}  className='image' src={urls?.regular} alt={alt_description} />
    ))}
  </div>
)
```
