---
title: 前端进阶（十三）
date: 2021-9-10 21:40:33
categories: IT
tags:
    - IT，Web
toc: true
thumbnail: https://cdn.kunkunzhang.top/velocityjs.jpeg
---

　　前端进阶（十三）-GIS

<!--more-->

## GoogleMap



```react
import { LoadScript, GoogleMap, Marker, InfoWindow } from '@react-google-maps/api'

const map = () => {
  return (
  	<GoogleMap
      center={center}
      zoom={zoom}
      mapContainerStyle={{
        width: '100%',
        height: '100%',
        ...mapContainerStyle,
      }}
      {...otherProps}
    >
      {children}
      <Marker
        key={`${coord.lat}-${coord.lng}`}
        position={coord}
        icon={{
          url,
          size,
        }}
        onClick={() => {
          setInfoCoord(coord)
        }}
      />
    </GoogleMap>
  )
}

```

Marker: 点

useGoogleMap: 对map进行操作

```react
import { useGoogleMap, useJsApiLoader } from '@react-google-maps/api'

const react = () => {
  const map = useGoogleMap()
  const { isLoaded } = useJsApiLoader({
    googleMapsApiKey: GOOGLE_MAP_API_KEY,
    libraries: ['drawing', 'places', 'visualization', 'marker'],
  })
  
  map.setCenter()
}

```

### mapbox-gl-js

https://github.com/mapbox/mapbox-gl-js



### deckgl

https://github.com/visgl/deck.gl



## Cesium

https://juejin.cn/post/7379909196340527113



## L7

https://l7.antv.antgroup.com/api/scene

https://zhuanlan.zhihu.com/p/499249093

## turf

地理分析库

https://turfjs.fenxianglu.cn/docs/api/center



## react-places-autocomplete

在react中基于google map自动补全地址

```react
import React from 'react';
import { GoogleMaps } from 'google-maps';
import PlacesAutocomplete from 'react-places-autocomplete';

class App extends React.Component {
    handleSelect = address => {
        console.log('Selected:', address);
    };

    render() {
        return (
            <PlacesAutocomplete
                wrapperElement={<div style={{ width: '200px' }} />}
                onSelect={this.handleSelect}
            >
                {({ getInputProps, suggestions, getSuggestionItemProps, loading }) => (
                    <input {...getInputProps({ placeholder: 'Search Places' })} />
                    {suggestions.map(suggestion => (
                        <div {...getSuggestionItemProps({ suggestion })}>
                            {suggestion.description}
                        </div>
                    ))}
                </PlacesAutocomplete>
            );
    }
}

```



## supercluster

聚合地图点展示



## markerclusterer

https://github.com/googlemaps/js-markerclusterer
