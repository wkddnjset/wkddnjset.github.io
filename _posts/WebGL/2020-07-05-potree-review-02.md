---
title: Potree 코드 분석
comments: true
description: Potree 논문을 기반으로 구현된 소스코드 중 일부를 리뷰한 내용을 정리해 보았다.
categories:
 - WebGL
tags: potree, webgl, vision 
---

# 아직 작성중 ..

## Point Cloud Loader

3개 중 하나의 로더를 사용해서 Point Cloud를 로드하며, `Potree.loadPointCloud` 함수를 통해 실행된다.

### EptLoader(*.json)

```javascript
class EptLoader {
	static async load(file, callback) {

		let response = await fetch(file);
		let json = await response.json();

		let url = file.substr(0, file.lastIndexOf('ept.json'));
		let geometry = new Potree.PointCloudEptGeometry(url, json);
		let root = new Potree.PointCloudEptGeometryNode(geometry);

		geometry.root = root;
		geometry.root.load();

		callback(geometry);
	}
};
```

ept.json 샘플 데이터 예시

> Entwine Point Tile(EPT)는 Octree 기반으로 저장된 Point Cloud 데이터 입니다.


```json
{
    "bounds": [634962.0, 848881.0, -1818.0, 639620.0, 853539.0, 2840.0],
    "boundsConforming": [635577.0, 848882.0, 406.0, 639004.0, 853538.0, 616.0],
    "dataType": "laszip",
    "hierarchyType": "json",
    "points": 10653336,
    "schema": [
        { "name": "X", "type": "signed", "size": 4, "scale": 0.01, "offset": 637291.0 },
        { "name": "Y", "type": "signed", "size": 4, "scale": 0.01, "offset": 851210.0 },
        { "name": "Z", "type": "signed", "size": 4, "scale": 0.01, "offset": 511.0 },
        { "name": "Intensity", "type": "unsigned", "size": 2 },
        { "name": "ReturnNumber", "type": "unsigned", "size": 1 },
        { "name": "NumberOfReturns", "type": "unsigned", "size": 1 },
        { "name": "ScanDirectionFlag", "type": "unsigned", "size": 1 },
        ...
    ],
    // 해상도를 나타내며, 2의 배수여야 함
    "span" : 256,
    "srs": {
        "authority": "EPSG",
        "horizontal": "3857",
        "vertical": "5703",
        "wkt": "PROJCS[\"WGS 84 ... AUTHORITY[\"EPSG\",\"3857\"]]"
    },
    "version" : "1.0.0"
}
```

### POCLoader(*.js)

### PointCloudArena4DGeometry(*.vpc)