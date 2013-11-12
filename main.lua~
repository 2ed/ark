local map -- stores tiledata
local mapWidth, mapHeight -- width and height in tiles

local mapX, mapY -- view x,y in tiles. can be a fractional value like 3.25.

local tilesDisplayWidth, tilesDisplayHeight -- number of tiles to show
local zoomX, zoomY

local tilesetImage
local tileSize -- size of tiles in pixels
local tileQuads = {} -- parts of the tileset used for different tiles
local tilesetSprite


function love.load()
   setupMap()
   setupMapView()
   setupTileset()
   screen = love.graphics.setMode( (tilesDisplayWidth-1)*zoomX*tileSize,( tilesDisplayHeight-1)*zoomY*tileSize)
end

function setupMap()
   mconf = love.filesystem.load("map/map.lua")()
   m = {}

   mapWidth = mconf.width
   mapHeight = mconf.height
   map = {} 
   for i = 1, 3 do 
      map[i] = {}
      m = mconf.layers[i].data
      for x=1,mapHeight do
	 map[i][x] = {}
	 for y=1,mapWidth do
	    map[i][x][y] = m[ ( x - 1 ) * mapWidth + y ]
	 end
	 map[i][x][mapWidth+1] = 12
      end
      map[i][mapHeight+1] = map[i][mapHeight]
   end
end

function setupMapView()
   mapX = 1
   mapY = 1
   tilesDisplayWidth = 5
   tilesDisplayHeight = 5
   
   zoomX = 2
   zoomY = 2
end

function setupTileset()
   tilesetImage = love.graphics.newImage("map/" .. tostring(mconf.tilesets[1].image))

   tilesetImage:setFilter("nearest", "nearest") -- this "linear filter" removes some artifacts if we were to scale the tiles
   tileSize = 18
   tilesW = mconf.tilesets[1].imagewidth
   tilesH = mconf.tilesets[1].imageheight
   
   tileSize = 18
   imWidth = tilesW/tileSize
   tileQuads = {}
   for i = 1, (tilesW/tileSize) * (tilesH/tileSize) do
      tileQuads[i] = love.graphics.newQuad(((i - 1) % imWidth) * tileSize , 
					   math.floor((i-1)/imWidth) * tileSize , tileSize, tileSize, tilesW, tilesH)
   end

   tilesetBatch = love.graphics.newSpriteBatch(tilesetImage, tilesDisplayWidth * tilesDisplayHeight)

   updateTilesetBatch(map)
end

function updateTilesetBatch(map)
   tilesetBatch:clear()
   for i = 2,3 do 
      for y=0, tilesDisplayHeight-1 do
	 for x=0, tilesDisplayWidth-1 do
	    local n = map[i][y+math.floor(mapY)][x+math.floor(mapX)]
	    if n ~= 0 then 
	       tilesetBatch:addq(tileQuads[n], x*tileSize, y*tileSize)
	    end
	 end
      end
   end
end

-- central function for moving the map
function moveMap(dx, dy)
   oldMapX = mapX
   oldMapY = mapY
   mapX = math.max(math.min(mapX + dx, mapWidth - tilesDisplayWidth + 2), 1)
   mapY = math.max(math.min(mapY + dy, mapHeight - tilesDisplayHeight+2), 1)
   -- only update if we actually moved
   if math.floor(mapX) ~= math.floor(oldMapX) or math.floor(mapY) ~= math.floor(oldMapY) then
      updateTilesetBatch(map)
   end
end

function love.update(dt)
   if love.keyboard.isDown("up")  then
      moveMap(0, -0.6 * tileSize * dt)
   end
   if love.keyboard.isDown("down")  then
      moveMap(0, 0.6 * tileSize * dt)
   end
   if love.keyboard.isDown("left")  then
      moveMap(-0.6 * tileSize * dt, 0)
   end
   if love.keyboard.isDown("right")  then
      moveMap(0.6 * tileSize * dt, 0)
   end
end

function love.draw()
   love.graphics.draw(tilesetBatch,
		      math.floor(-zoomX*(mapX%1)*tileSize), math.floor(-zoomY*(mapY%1)*tileSize),
		      0, zoomX, zoomY)
   love.graphics.print("FPS: "..love.timer.getFPS(), 10, 20)
end
