# N24DCDT055_NGUYEN-TRAN-TRUNG-NGHIA
Nhiệm vụ 1: Sắp xếp trộn (merge_sort)
function merge_sort(a::AbstractVector, before::Function)
    if length(a) <= 1
        return copy(a)
    end
    mid = div(length(a), 2)
    left = merge_sort(a[1:mid], before)
    right = merge_sort(a[mid+1:end], before)
    
    result = similar(a, 0)
    sizehint!(result, length(a))
    i, j = 1, 1
    while i <= length(left) && j <= length(right)
        if before(right[j], left[i])
            push!(result, right[j])
            j += 1
        else
            push!(result, left[i])
            i += 1
        end
    end
    append!(result, left[i:end])
    append!(result, right[j:end])
    return result
End
Nhiệm vụ 2: Tìm kiếm nhị phân (binary_search)
function binary_search(a::Vector{Order}, id::Int)
    left, right = 1, length(a)
    
    while left <= right
        mid = left + div(right - left, 2)
        
        if a[mid].id == id
            return a[mid]
        elseif a[mid].id < id
            left = mid + 1
        else
            right = mid - 1
        end
    end
    
    return nothing
end

Nhiệm vụ 3: Thuật toán BFS và thời gian giao đơn (bfs_routes)
function bfs_routes(graph, source=1)
    n = length(graph)
    distance = fill(-1, n)
    parent = fill(0, n)
    routes = [Int[] for _ in 1:n]
    
    queue = Int[]
    distance[source] = 0
    push!(queue, source)
    
    while !isempty(queue)
        u = popfirst!(queue)
        for v in graph[u]
            if distance[v] == -1
                distance[v] = distance[u] + 1
                parent[v] = u
                push!(queue, v)
            end
        end
    end
    
    for v in 1:n
        if distance[v] != -1
            path = Int[]
            curr = v
            while curr != 0
                push!(path, curr)
                curr = parent[curr]
            end
            routes[v] = reverse!(path)
        end
    end
    
    return (distance=distance, parent=parent, routes=routes)
End

Nhiệm vụ 4: Quy hoạch động chọn đơn (knapsack)
function knapsack(orders, costs, budget)
    n = length(orders)
    table = zeros(Int, n + 1, budget + 1)
    
    for i in 1:n
        id = orders[i].id
        c = get(costs, id, 0)
        v = orders[i].benefit
        for b in 0:budget
            if c > b
                table[i+1, b+1] = table[i, b+1]
            else
                table[i+1, b+1] = max(table[i, b+1], v + table[i, b-c+1])
            end
        end
    end
    
    value = table[n+1, budget+1]
    
    # Truy vết tìm các đơn được chọn
    ids = Int[]
    b = budget
    for i in n:-1:1
        id = orders[i].id
        c = get(costs, id, 0)
        v = orders[i].benefit
        if b >= c && table[i+1, b+1] == v + table[i, b-c+1]
            push!(ids, id)
            b -= c
        end
    end
    reverse!(ids)
    
    cost = sum(get(costs, id, 0) for id in ids)
    
    return (ids=ids, value=value, cost=cost, table=table)
end
