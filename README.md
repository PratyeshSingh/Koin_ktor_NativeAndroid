

import de.jensklingenberg.ktorfit.Ktorfit
import de.jensklingenberg.ktorfit.converter.Converter
import de.jensklingenberg.ktorfit.internal.TypeData
import io.ktor.client.statement.HttpResponse
import java.lang.reflect.Type

class ApiResultConverterFactory : Converter.Factory {
    override fun suspendConverter(
        typeData: TypeData, 
        ktorfit: Ktorfit
    ): Converter.Suspend<HttpResponse, Any>? {
        // Check if the return type is ApiResult
        if (typeData.typeInfo.type == ApiResult::class) {
            return object : Converter.Suspend<HttpResponse, Any> {
                override suspend fun convert(response: HttpResponse): Any {
                    return try {
                        val status = response.status
                        if (status.isSuccess()) {
                            // Extract the actual data type (T) from ApiResult<T>
                            val innerType = typeData.typeArgs.first().typeInfo
                            val data = response.body(innerType)
                            ApiResult.Success(data, status)
                        } else {
                            // Use your existing error handler logic
                            ApiResult.Failure(message = status.description, statusCode = status)
                        }
                    } catch (e: Exception) {
                        ApiResult.Failure(message = e.message, throwable = e)
                    }
                }
            }
        }
        return null
    }
}


------------------------------------------------
------------------------------------------------


import de.jensklingenberg.ktorfit.Ktorfit
import de.jensklingenberg.ktorfit.converter.Converter
import de.jensklingenberg.ktorfit.internal.TypeData
import io.ktor.client.statement.HttpResponse
import java.lang.reflect.Type

class ApiResultConverterFactory : Converter.Factory {
    override fun suspendConverter(
        typeData: TypeData, 
        ktorfit: Ktorfit
    ): Converter.Suspend<HttpResponse, Any>? {
        // Check if the return type is ApiResult
        if (typeData.typeInfo.type == ApiResult::class) {
            return object : Converter.Suspend<HttpResponse, Any> {
                override suspend fun convert(response: HttpResponse): Any {
                    return try {
                        val status = response.status
                        if (status.isSuccess()) {
                            // Extract the actual data type (T) from ApiResult<T>
                            val innerType = typeData.typeArgs.first().typeInfo
                            val data = response.body(innerType)
                            ApiResult.Success(data, status)
                        } else {
                            // Use your existing error handler logic
                            ApiResult.Failure(message = status.description, statusCode = status)
                        }
                    } catch (e: Exception) {
                        ApiResult.Failure(message = e.message, throwable = e)
                    }
                }
            }
        }
        return null
    }
}


------------------------------------------------
------------------------------------------------

val ktorfit = Ktorfit.Builder()
    .baseUrl(BASE_URL)
    .httpClient(httpClient)
    .converterFactories(ApiResultConverterFactory()) // Register here
    .build()



------------------------------------------------
------------------------------------------------

interface PetcoService {
    @GET("cart")
    suspend fun getCart(): ApiResult<CartResponse>
}
