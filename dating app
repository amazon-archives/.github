provider "aws" {
  region = "us-west-2"
}

resource "aws_dynamodb_table" "users" {
  name           = "users"
  read_capacity  = 5
  write_capacity = 5

  hash_key = "email"

  attribute {
    name = "email"
    type = "S"
  }
}

resource "aws_cognito_user_pool" "user_pool" {
  name = "user_pool"
}

resource "aws_cognito_user_pool_client" "user_pool_client" {
  name                 = "user_pool_client"
  user_pool_id         = aws_cognito_user_pool.user_pool.id
  explicit_auth_flows = ["ADMIN_NO_SRP_AUTH"]
}

resource "aws_cognito_identity_pool" "identity_pool" {
  identity_pool_name = "identity_pool"

  allow_unauthenticated_identities = false

  cognito_identity_providers {
    client_id = aws_cognito_user_pool_client.user_pool_client.client_id
    provider_name = aws_cognito_user_pool.user_pool.endpoint
  }
}

resource "aws_api_gateway_rest_api" "api_gateway_rest_api" {
  name = "api_gateway_rest_api"
}

resource "aws_api_gateway_resource" "api_gateway_resource" {
  rest_api_id = aws_api_gateway_rest_api.api_gateway_rest_api.id
  parent_id   = aws_api_gateway_rest_api.api_gateway_rest_api.root_resource_id
  path_part   = "user"
}

resource "aws_api_gateway_method" "api_gateway_method" {
  rest_api_id   = aws_api_gateway_rest_api.api_gateway_rest_api.id
  resource_id   = aws_api_gateway_resource.api_gateway_resource.id
  http_method   = "GET"
  authorization = "NONE"
}

resource "aws_lambda_function" "lambda_function" {
  filename      = "function.zip"
  function_name = "lambda_function"
  role          = aws_iam_role.lambda_role.arn
  handler       = "index.handler"
  runtime       = "nodejs12.x"
}

resource "aws_iam_role" "lambda_role" {
  name = "lambda_role"

  assume_role_policy = jsonencode({
    Version = "2012-10-17",
    Statement = [
      {
        Action = "sts:AssumeRole",
        Effect = "Allow",
        Principal = {
          Service = "lambda.amazonaws.com"
        }
