# 在 Elm 中显示和编辑谷歌地图

> 原文：<https://dev.to/rinn7e/display-and-edit-google-map-inelm-dg1>

在 elm 中使用谷歌地图有点棘手。本教程将展示如何初始化地图，编辑地图，并通过端口获取地图数据。

预告:[https://chmar77.github.io/elm-google-map-tutorial/](https://chmar77.github.io/elm-google-map-tutorial/)

## 要求

本教程面向那些已经熟悉 elm 的人。

所需的工具:

*   榆树:`npm install -g elm`
*   elm-live: `npm install -g elm-live`

## 设置项目

*   创建以下文件夹结构

```
elm-google-map
â”œâ”€â”€ src
â”‚   â”œâ”€â”€ Main.elm
â”‚   â”œâ”€â”€ Map.elm
â”‚   â””â”€â”€ Port.elm
â””â”€â”€ index.html 
```

*   在 index.html，复制以下代码

```
<!DOCTYPE html>
<html>
  <head>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    Elm Google Map
    <style>
    html, body{
        margin:0;
        padding:0;
    }
    #map {
        height:300px;
        width:300px;
    }
    #edit-map {
        height:300px;
        width:300px;
    }
    .hidden{
        visibility: hidden;
        height:0;
        width:0;
    }
    </style>
  </head>

  <body>
    <script src="dist/elm.js"></script>
    <script>
    var app = Elm.Main.fullscreen();

    </script>
  </body>
</html> 
```

*   运行:`elm-package install`
*   在 Main.elm 中复制以下代码

```
module Main exposing (..)

import Html exposing (..)
import Html.Attributes exposing (..)
import Html.Events exposing (..)

main : Program Never Model Msg
main =
    Html.program
        { init = init
        , update = update
        , view = view
        , subscriptions = subscriptions
        }

type alias Model =
    { title : String
    }

init : ( Model, Cmd Msg )
init =
    ( { title = "Elm Google Map" }, Cmd.none )

type Msg
    = NoOp

update : Msg -> Model -> ( Model, Cmd Msg )
update msg model =
    case msg of
        NoOp ->
            ( model, Cmd.none )

view : Model -> Html Msg
view model =
    div []
        [ h1 [] [ text "Elm Google Map" ]
        ]

subscriptions : Model -> Sub Msg
subscriptions model =
    Sub.none 
```

*   在 elm-package.json 中，将源目录从"."致“src”

```
 "source-directories": [
        "src"
 ], 
```

*   在我们项目的根文件夹中运行`elm-live src/Main.elm --output=dist/elm.js`,进入 [http://localhost:8000/](http://localhost:8000/) 查看我们初始化的项目

*   如果你看到“Elm 谷歌地图”,那就意味着一切正常

## 创建地图模块

*   复制以下代码:

```
module Map exposing (Model, JsObject, init, modify, toJsObject)

type Model
    = Internal
        { latitude : Float
        , longtitude : Float
        }

init : Model
init =
    Internal
        { latitude = 11.55408504200135
        , longtitude = 104.910961602369
        }

modify : Float -> Float -> Model -> Model
modify latitude longtitude (Internal model) =
    Internal
        { model
            | latitude = latitude
            , longtitude = longtitude
        }

type alias JsObject =
    { lat : Float
    , lng : Float
    }

toJsObject : Model -> JsObject
toJsObject (Internal model) =
    { lat = model.latitude
    , lng = model.longtitude
    } 
```

在这里，我们试图使我们的模块隐藏。使用模型类型的记录，在访问或修改时将需要来自该模块的函数。它不能直接访问记录。

## 端口模块

```
port module Port exposing (..)

import Map

-- Outgoing Port

port initializeMap : Map.JsObject -> Cmd msg

port initializeEditMap : Map.JsObject -> Cmd msg

port moveMap : Map.JsObject -> Cmd msg

-- Incoming Port

port mapMoved : (Map.JsObject -> msg) -> Sub msg 
```

这里我们将输出端口分为两部分，输入和输出。

## Javascript 部分

*   在 elm.js 脚本下添加 google 地图脚本

```
<script src="https://maps.googleapis.com/maps/api/js?key={{your-api-key}}"></script> 
```

*   在 index.html 通过下面的`var app = Elm.Main.fullscreen();`代码

```
app.ports.initializeMap.subscribe(function (pos) {
    console.log("Initialize Map")
    var mapDiv = document.getElementById('map');
    console.log(pos);
    if (mapDiv) {
    // Map
    var myLatlng = new google.maps.LatLng(pos);
    var mapOptions = {
        zoom: 15,
        center: myLatlng
    };
    var gmap = new google.maps.Map(mapDiv, mapOptions);

    // Marker
    var marker = new google.maps.Marker({
        position: myLatlng,
        title: "Hello World!"
    });
    marker.setMap(gmap);

    // Listening for map move event
    app.ports.moveMap.subscribe(function (pos) {
        console.log("received", pos);
        var myLatlng = new google.maps.LatLng(pos);
        gmap.setCenter(myLatlng);
        marker.setPosition(myLatlng)
    });

    } else {
    console.log ("Cant find map dom");
    }

}); 
```

在这里，我们只是创建一个事件来监听 elm 的 initializeMap 端口。当我们稍后尝试编辑地图时，将使用 moveMap 事件。

## 回到主模块

*   我们可以导入地图和端口模块

```
import Map
import Port 
```

*   更新我们的模型，并初始化

```
type alias Model =
    { title : String
    , map : Map.Model
    } 
```

```
init : ( Model, Cmd Msg )
init =
    ( { title = "Elm Google Map"
      , map = Map.init
      }
    , Map.init
        |> Map.toJsObject
        |> Port.initializeMap
    ) 
```

*   最后更新我们的观点

```
view : Model -> Html Msg
view model =
    div []
        [ h1 [] [ text model.title ]
        , p [] [ text <| "Current pointer" ++ (toString <| Map.toJsObject model.map) ]
        , div []
            [ div [ id "map" ] []
            ]
        ] 
```

*   如果您没有运行 elm-live，请再次运行`elm-live src/Main.elm --output=dist/elm.js`并转到 [http://localhost:8000/](http://localhost:8000/)
*   如果您能够看到地图，那么一切都在按预期工作

## 编辑地图

*   首先在 index.html 的前一个代码下面添加额外的 javascript 代码

```
app.ports.initializeEditMap.subscribe(function (pos) {
    console.log("Initialize Edit Map")
    var mapDiv = document.getElementById('edit-map');
    console.log(pos);

    if (mapDiv) {
        // Map
        var myLatlng = new google.maps.LatLng(pos);
        var mapOptions = {
            zoom: 15,
            center: myLatlng
        };
        var gmap = new google.maps.Map(mapDiv, mapOptions);

        // Marker
        var marker = new google.maps.Marker({
            position: myLatlng,
            title: "Hello World!"
        });
        marker.setMap(gmap);

        gmap.addListener('drag', function () {
            var newPos = {
            lat: gmap.getCenter().lat(),
            lng: gmap.getCenter().lng()
            };

            marker.setPosition(newPos);

            app.ports.mapMoved.send(newPos);
        });

    } else {
        console.log ("Cant find edit map dom");
    }

}); 
```

*   回到 Main.elm，将 State type 添加到我们的模型中，以便我们可以编辑地图

```
type alias Model =
    { title : String
    , map : Map.Model
    , state : State
    }

type State
    = View
    | Edit

init : ( Model, Cmd Msg )
init =
    ( { title = "Elm Google Map"
      , map = Map.init
      , state = View
      }
    , Map.init
        |> Map.toJsObject
        |> Port.initializeMap
    ) 
```

*   然后在视图功能中添加编辑按钮和编辑视图

```
view : Model -> Html Msg
view model =
    div []
        [ h1 [] [ text model.title ]
        , p [] [ text <| "Current pointer" ++ (toString <| Map.toJsObject model.map) ]
        , div []
            [ div [ id "map" ] []
            , button [ onClick EditMap ] [ text "Edit" ]
            ]
        , editView model
        ] 
```

*   为 editView 复制以下代码

```
editView : Model -> Html Msg
editView model =
    div
        [ class <|
            case model.state of
                View ->
                    "hidden"

                Edit ->
                    ""
        ]
        [ hr [] []
        , div [ id "edit-map" ] []
        , button [ onClick SaveEditMap ] [ text "Done" ]
        ] 
```

*   我们还想在编辑时监听拖动事件。我们可以通过修改我们的订阅来实现

```
subscriptions : Model -> Sub Msg
subscriptions model =
    Port.mapMoved OnEditMapDrag 
```

*   正如你所看到的，我们创建了一些新的消息，我们必须创建和处理它们

```
type Msg
    = NoOp
    | EditMap
    | OnEditMapDrag Map.JsObject
    | SaveEditMap 
```

*   在我们的更新函数中

```
update : Msg -> Model -> ( Model, Cmd Msg )
update msg model =
    case msg of
        NoOp ->
            ( model, Cmd.none )

        EditMap ->
            ( { model | state = Edit }
            , model.map
                |> Map.toJsObject
                |> Port.initializeEditMap
            )

        OnEditMapDrag { lat, lng } ->
            ( { model | map = Map.modify lat lng model.map }
            , Cmd.none
            )

        SaveEditMap ->
            ( { model | state = View }
            , model.map
                |> Map.toJsObject
                |> Port.moveMap
            ) 
```

## 这就是了

源代码在这里:[https://github.com/chmar77/elm-google-map-tutorial](https://github.com/chmar77/elm-google-map-tutorial)
预览:[https://chmar77.github.io/elm-google-map-tutorial/](https://chmar77.github.io/elm-google-map-tutorial/)

教程到此结束。如果有些事情对你不起作用，或者你不明白的某些事情需要解释，请在下面评论。

感谢阅读！！！